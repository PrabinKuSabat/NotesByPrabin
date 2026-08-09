# Why Docker is needed

Docker is not the checker. It provides the controlled Ubuntu 24.04 build environment.

Your Ubuntu 26.04 RISC-V libraries may already contain RVA23/Zcb code. Adding `-march=…` only controls newly compiled BusyBox source; it cannot change instructions already present in static `libc.a`, startup objects, or `libgcc.a`.

Canonical made RVA23 the Ubuntu RISC-V baseline starting with 25.10, while 24.04 retains compatibility with older hardware. [Canonical’s RISC-V baseline explanation](https://canonical.com/blog/canonical-and-ubuntu-risc-v-a-2025-retro-and-looking-forward-to-2026)

Docker will:

- Run an Ubuntu 24.04 x86-64 container.
- Install an Ubuntu 24.04 RISC-V cross-compiler and sysroot.
- Cross-compile BusyBox for RISC-V.
- Delete the temporary container afterward.
- Preserve the build results through `$RV2_WORK`.

It does not emulate RISC-V or test the board. The checks are:

- `readelf`: ELF ISA attributes.
- `objdump`: scan for Zcb instructions.
- QEMU: execution test with `zcb=false`.
- RV2: final hardware test.

GCC also documents that `-march` and `-mabi` defaults are system-dependent, which is why we specify both explicitly. [GCC RISC-V options](https://gcc.gnu.org/onlinedocs/gcc-15.2.0/gcc/RISC-V-Options.html)

# 1. Check your existing environment

Paste this first:

```bash
printf 'RV2_WORK=%s\n' "${RV2_WORK:-NOT SET}"

if [ -z "${RV2_WORK:-}" ] || \
   [ "$RV2_WORK" = "/" ] || \
   [ ! -d "$RV2_WORK/src" ]; then
    echo "STOP: RV2_WORK is missing or incorrect"
else
    export RV2_WORK

    mkdir -p \
        "$RV2_WORK/configs" \
        "$RV2_WORK/logs" \
        "$RV2_WORK/artifacts"

    test -x "$RV2_WORK/rootfs/init"

    echo "Preflight path PASS"
fi

command -v git
command -v cpio
command -v gzip

df -h "$RV2_WORK"
```

You must see:

```text
Preflight path PASS
```

If `RV2_WORK` shows `NOT SET`, first run this with your real existing path:

```bash
export RV2_WORK="/your/actual/rv2-work/path"
```

Do not literally use the placeholder path.

Also, use normal names such as `$RV2_WORK`. Do not include Markdown escape characters such as `$RV2\_WORK` or `https\://`.

# 2. Check Docker

Paste:

```bash
command -v docker
sudo docker info >/dev/null
sudo docker version
```

If the daemon is not running:

```bash
sudo systemctl start docker
sudo docker info >/dev/null
```

If `docker` is not installed, stop here and install Docker Engine using the [official Ubuntu instructions](https://docs.docker.com/engine/install/ubuntu/). Docker officially supports Ubuntu 26.04. Because your network uses Sophos SSL inspection, do not disable TLS verification if installation or image pulling reports a certificate error—send me that error instead.

Now download and record the Ubuntu 24.04 image:

```bash
sudo docker pull ubuntu:24.04

sudo docker image inspect \
    --format '{{index .RepoDigests 0}}' \
    ubuntu:24.04 |
    tee "$RV2_WORK/configs/busybox-1.38.0-build-image.txt"
```

# 3. Prepare and verify the BusyBox source

Paste this entire block:

```bash
bash -euo pipefail <<'HOST'
: "${RV2_WORK:?RV2_WORK is not exported}"

BB_REPO="https://github.com/vda-linux/busybox_mirror.git"
BB_COMMIT="fc71374dfccd46448c62947269a35f1420d7ee28"
BB_SRC="$RV2_WORK/src/busybox"
BB_CONFIG="$RV2_WORK/configs/busybox-1.38.0-riscv64.config"
NEW_ROOTFS="$RV2_WORK/rootfs-rv2-1.38.0"

test -x "$RV2_WORK/rootfs/init"
test ! -e "$NEW_ROOTFS"

install -m 0755 \
    "$RV2_WORK/rootfs/init" \
    "$RV2_WORK/configs/init"

if [ -f "$BB_CONFIG" ] && \
   [ ! -e "$BB_CONFIG.before-rv2" ]; then
    cp -a "$BB_CONFIG" "$BB_CONFIG.before-rv2"
fi

if [ ! -d "$BB_SRC/.git" ]; then
    git clone "$BB_REPO" "$BB_SRC"
fi

cd "$BB_SRC"

if ! git diff --quiet || ! git diff --cached --quiet; then
    echo "STOP: BusyBox has modified tracked source files"
    exit 1
fi

if ! git cat-file -e "$BB_COMMIT^{commit}" 2>/dev/null; then
    git fetch origin
fi

git checkout --detach "$BB_COMMIT"

test "$(git rev-parse HEAD)" = "$BB_COMMIT"

grep -qx 'VERSION = 1' Makefile
grep -qx 'PATCHLEVEL = 38' Makefile
grep -qx 'SUBLEVEL = 0' Makefile

git rev-parse HEAD \
    > "$RV2_WORK/configs/busybox-1.38.0.commit"

echo "BusyBox source pin PASS: $(git rev-parse HEAD)"
echo "Saved /init: $RV2_WORK/configs/init"
HOST
```

Expected final lines:

```text
BusyBox source pin PASS: fc71374...
Saved /init: ...
```

# 4. Build BusyBox in Docker

Paste the entire block below. It may take several minutes. The final `CONTAINER` line must be pasted exactly and must be alone on its line.

```bash
sudo docker run --rm -i \
    --env HOST_UID="$(id -u)" \
    --env HOST_GID="$(id -g)" \
    --volume "$RV2_WORK:/work" \
    --workdir /work/src/busybox \
    ubuntu:24.04 \
    bash -s <<'CONTAINER'
set -euxo pipefail

export DEBIAN_FRONTEND=noninteractive
export ARCH=riscv
export CROSS_COMPILE=riscv64-linux-gnu-

TARGET_MARCH="rv64gc_zba_zbb_zbs"
TARGET_ABI="lp64d"
TARGET_FLAGS="-march=$TARGET_MARCH -mabi=$TARGET_ABI"

CONFIG_NAME="busybox-1.38.0-riscv64.config"
CONFIG_PATH="/work/configs/$CONFIG_NAME"
BUILD_LOG="/work/logs/busybox-1.38.0-rv2-build.log"
ROOTFS="/work/rootfs-rv2-1.38.0"

cleanup_ownership()
{
    chown -R "$HOST_UID:$HOST_GID" \
        /work/src/busybox \
        /work/rootfs-rv2-1.38.0 \
        /work/configs \
        /work/logs \
        2>/dev/null || true
}

trap cleanup_ownership EXIT

test ! -e "$ROOTFS"

apt-get update

apt-get install -y --no-install-recommends \
    build-essential \
    binutils-riscv64-linux-gnu \
    file \
    gcc-riscv64-linux-gnu \
    libc6-dev-riscv64-cross

make ARCH="$ARCH" \
    CROSS_COMPILE="$CROSS_COMPILE" \
    distclean

make ARCH="$ARCH" \
    CROSS_COMPILE="$CROSS_COMPILE" \
    defconfig

grep -qx '# CONFIG_STATIC is not set' .config

sed -i \
    -e 's/^# CONFIG_STATIC is not set$/CONFIG_STATIC=y/' \
    -e 's/^CONFIG_TC=y$/# CONFIG_TC is not set/' \
    -e "s|^CONFIG_EXTRA_CFLAGS=.*|CONFIG_EXTRA_CFLAGS=\"$TARGET_FLAGS\"|" \
    .config

make ARCH="$ARCH" \
    CROSS_COMPILE="$CROSS_COMPILE" \
    oldconfig </dev/null

grep -qx 'CONFIG_STATIC=y' .config
grep -qx '# CONFIG_TC is not set' .config
grep -qx \
    "CONFIG_EXTRA_CFLAGS=\"$TARGET_FLAGS\"" \
    .config

cp .config "$CONFIG_PATH"

(
    cd /work/configs
    sha256sum "$CONFIG_NAME" \
        > "$CONFIG_NAME.sha256"
)

{
    dpkg-query -W \
        binutils-riscv64-linux-gnu \
        gcc-riscv64-linux-gnu \
        libc6-dev-riscv64-cross

    "$CROSS_COMPILE"gcc --version
    "$CROSS_COMPILE"gcc -dumpmachine
    "$CROSS_COMPILE"gcc -print-sysroot
    "$CROSS_COMPILE"ld --version
} > /work/configs/busybox-1.38.0-toolchain.txt

make ARCH="$ARCH" \
    CROSS_COMPILE="$CROSS_COMPILE" \
    V=1 \
    -j"$(nproc)" \
    2>&1 |
    tee "$BUILD_LOG"

observed_march="$(
    grep -oE -- '-march=[^[:space:]]+' "$BUILD_LOG" |
        sort -u
)"

observed_mabi="$(
    grep -oE -- '-mabi=[^[:space:]]+' "$BUILD_LOG" |
        sort -u
)"

test "$observed_march" = "-march=$TARGET_MARCH"
test "$observed_mabi" = "-mabi=$TARGET_ABI"

make ARCH="$ARCH" \
    CROSS_COMPILE="$CROSS_COMPILE" \
    CONFIG_PREFIX="$ROOTFS" \
    install

install -m 0755 \
    /work/configs/init \
    "$ROOTFS/init"

mkdir -p "$ROOTFS"/{dev,proc,sys,tmp}
chmod 1777 "$ROOTFS/tmp"

BB="$ROOTFS/bin/busybox"

test -x "$BB"
test -L "$ROOTFS/bin/sh"

file "$BB" |
    tee /work/logs/busybox-1.38.0-file.txt

grep -q 'statically linked' \
    /work/logs/busybox-1.38.0-file.txt

"$CROSS_COMPILE"readelf -hW "$BB" \
    > /work/logs/busybox-1.38.0-elf-header.txt

grep -q 'Machine:.*RISC-V' \
    /work/logs/busybox-1.38.0-elf-header.txt

"$CROSS_COMPILE"readelf -lW "$BB" \
    > /work/logs/busybox-1.38.0-program-headers.txt

if grep -q 'INTERP' \
    /work/logs/busybox-1.38.0-program-headers.txt; then
    echo "BUSYBOX GATE FAIL: dynamically linked"
    exit 1
fi

"$CROSS_COMPILE"readelf -A "$BB" \
    > /work/logs/busybox-1.38.0-attributes.txt

cat /work/logs/busybox-1.38.0-attributes.txt

if grep -Eiq \
    'zcb|zcmop|zimop|zfa|zawrs|zihintntl|zvbb|zicbop|supm' \
    /work/logs/busybox-1.38.0-attributes.txt; then
    echo "BUSYBOX ISA GATE FAIL: unsupported RV2 extension"
    exit 1
fi

"$CROSS_COMPILE"objdump \
    -d -M no-aliases "$BB" \
    > /tmp/busybox.disassembly

zcb_regex='[[:space:]]c\.(lbu|lhu|lh|sb|sh|zext\.b|sext\.b|zext\.h|sext\.h|zext\.w|not|mul)([[:space:]]|$)'

if grep -E "$zcb_regex" \
    /tmp/busybox.disassembly \
    > /work/logs/busybox-1.38.0-zcb-matches.txt; then
    echo "BUSYBOX ISA GATE FAIL: Zcb instruction found"
    cat /work/logs/busybox-1.38.0-zcb-matches.txt
    exit 1
fi

sha256sum "$BB" \
    > /work/logs/busybox-1.38.0.sha256

echo "BUSYBOX ISA GATE PASS: RV2-compatible, static, no Zcb" |
    tee /work/logs/busybox-1.38.0-rv2-status.txt
CONTAINER
```

Do not continue unless the final line is:

```text
BUSYBOX ISA GATE PASS: RV2-compatible, static, no Zcb
```

Your original `$RV2_WORK/rootfs` has not been removed or changed.

# 5. Package the new rootfs

After the BusyBox gate passes, paste:

```bash
bash -euo pipefail <<'HOST'
: "${RV2_WORK:?RV2_WORK is not exported}"

NEW_ROOTFS="$RV2_WORK/rootfs-rv2-1.38.0"
INITRAMFS="$RV2_WORK/artifacts/rootfs-rv2-busybox-1.38.0.cpio.gz"

command -v cpio >/dev/null
command -v gzip >/dev/null

test -x "$NEW_ROOTFS/init"
test -x "$NEW_ROOTFS/bin/busybox"
test -L "$NEW_ROOTFS/bin/sh"

find "$NEW_ROOTFS" \
    -exec touch -h -d '@0' {} +

(
    cd "$NEW_ROOTFS"

    find . -print0 |
        LC_ALL=C sort -z |
        cpio --null -o \
            --format=newc \
            --owner=0:0 \
            2>/dev/null
) |
    gzip -9n > "$INITRAMFS"

gzip -t "$INITRAMFS"
sha256sum "$INITRAMFS" |
    tee "$INITRAMFS.sha256"

ls -lh "$INITRAMFS"

echo "INITRAMFS PACKAGE PASS"
HOST
```

# 7. Make the new rootfs the active one

Only after QEMU passes:

```bash
bash -euo pipefail <<'HOST'
: "${RV2_WORK:?RV2_WORK is not exported}"

CURRENT="$RV2_WORK/rootfs"
NEW="$RV2_WORK/rootfs-rv2-1.38.0"
BACKUP="$RV2_WORK/rootfs-rva23-zcb-broken"

test -d "$CURRENT"
test -x "$NEW/bin/busybox"
test -x "$NEW/init"
test ! -e "$BACKUP"

mv "$CURRENT" "$BACKUP"

if ! mv "$NEW" "$CURRENT"; then
    mv "$BACKUP" "$CURRENT"
    echo "Activation failed; original rootfs restored"
    exit 1
fi

file "$CURRENT/bin/busybox"
ls -l "$CURRENT/bin/sh"

echo "Active rootfs replacement PASS"
echo "Old failing rootfs preserved at: $BACKUP"
HOST
```

For now, run through the Docker build and send me the output starting at `Tag_RISCV_arch` through the final BusyBox gate result. If any block fails, stop at that block—especially before rootfs activation.
