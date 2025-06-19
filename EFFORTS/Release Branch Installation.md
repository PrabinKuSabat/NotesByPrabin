1. Satisfy all the dependencies from GitHub.
2. Ensure that the gcc and gxx are below min versions satisfied by yor cuda And cuda is below 11.0.1
	- Open Activation Script
	  nano $CONDA_PREFIX/etc/conda/activate.d/env_vars.sh
	- Add at top
	  export PATH="$CONDA_PREFIX/bin: $PATH"
	- Restart environment and check again
	  conda deactivate && conda activate accelRelease
	  which gcc
