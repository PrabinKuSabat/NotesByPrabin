# TPTPU-sim

1. Architecture:
	- Version 1:  
		DRAM -(Interconnect)> Weight Fetcher  
		CPU Interface -(Interconnect)> Unified Buffer  
		Weight Fetcher and Unified Buffer -(Interconnect)> Matrix Multiply Unit  
		Consists of 5 bold face units along with a controller.

		> [!note] What is a bold face unit

	- Version 2:
	- DRAM -(Interconnect)> Weight Fetcher  
	  DRAM -(Interconnect)> Unified Buffer  
	  Rest all same as V1.
