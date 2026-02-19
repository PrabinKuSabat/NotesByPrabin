# Making Sophos Login

- First install the certificate
	- sudo cp your-cert.pem /usr/local/share/ca-certificates/your-cert.crt
	- sudo update-ca-certificates
- sudo nano /usr/local/bin/autologin.sh

```
#!/bin/bash
# Your curl command here
curl -v \
  -H "Content-Type: application/x-www-form-urlencoded" \
  -H "Referer: http://10.110.1.1" \
  --data-urlencode "mode=191" \
  --data-urlencode "username=username" \
  --data-urlencode "password=password" \
  --data-urlencode "a=$(date +%s%3N)" \
  --data-urlencode "producttype=0" \
  http://10.110.1.1

```

`sudo chmod +x /usr/local/bin/autologin.sh`

Check Using : `curl -Ik https://www.google.com`  
`sudo apt install aria2 -y`  
`aria2c -x 16 -s 16 --check-certificate=false "https://www.kernel.org/pub/linux/kernel/v6.x/linux-6.6.tar.xz"`

# Dependencies

libelf-dev  
sudo apt-get update  
sudo apt-get install  
	 python3-dev  
	 libdw-dev  
	 libaudit-dev  
	 libslang2-dev  
	 libperl-dev  
	 systemtap-sdt-dev  
	 libelf-dev

sudo apt-get install libtraceevent-dev libcap-dev libnuma-dev libbabeltrace-ctf-dev libpfm4-dev  
sudo apt-get update  
sudo apt-get install pkg-config

`sudo cp perf /usr/local/bin/`

```
# Allow users to collect data (sets paranoid level to -1)
sudo sysctl -w kernel.perf_event_paranoid=-1

# Allow access to kernel addresses
sudo sysctl -w kernel.kptr_restrict=0
```
