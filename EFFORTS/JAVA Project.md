# TO-Do

- [x] Find the xxHash implementation in Java ✅ 2025-03-27
- [x] Implement the FlipHash in java ✅ 2025-03-27
	- [x] Convert the c code to java ✅ 2025-03-19
	- [x] Make the fliphash write the hashes to a cache file. ✅ 2025-06-11
- [x] Make a thread run as a socket. ✅ 2025-03-27
- [x] Make FlipHash work on the IP and port numbers. ✅ 2025-03-27
	- [-] Each new server added to the server list should be considered as a new thread **[server thread]**.
		- [x] Modified: Each new server adds itself to the load-balancer. ✅ 2025-03-27
			- [x] Run check ✅ 2025-06-11
	- [-] The hashed output should send the client request to that server thread.
		- [x] The hashed output should be used to select the backend server ✅ 2025-03-27
		- [x] send the client jar file to the backend server for processing. ✅ 2025-03-27
		- [x] Run check ✅ 2025-06-11
- [-] Allow clients to access the server. ✅ 2025-03-27
	- [x] Allow clients to access the backend servers. ✅ 2025-03-28
	- [x] Allow clients to send files to the backend servers ✅ 2025-03-28
	- [x] Run check ✅ 2025-06-11
- [x] Backend Servers should run the jar files in a protected environment. ✅ 2025-06-11
	- [x] Client receiving output directly from the backend server. ✅ 2025-03-28
		- [x] Run check ✅ 2025-06-11
- [-] A new thread is being generated for each server.
	- [x] Client being run as thread in the backends. ✅ 2025-03-28
	- [x] run check ✅ 2025-06-11
- [x] Make a GUI ✅ 2025-06-11
	- [x] show the no. of servers active. **[as icons of servers]** ✅ 2025-06-11
	- [x] Show the load on the servers. **[Basically the no. of requests waiting on that.]** ✅ 2025-06-11
	- [x] If the server list increases beyond 25, convert the GUI to a spread sheet. ✅ 2025-06-11

# Extra

- [x] Implement Dictionary using FlipHash. ✅ 2026-01-28
