# TO-Do

- [x] Find the xxHash implementation in Java ✅ 2025-03-27
- [x] Implement the FlipHash in java ✅ 2025-03-27
	- [x] Convert the c code to java ✅ 2025-03-19
	- [ ] Make the fliphash write the hashes to a cache file.
- [x] Make a thread run as a socket. ✅ 2025-03-27
- [x] Make FlipHash work on the IP and port numbers. ✅ 2025-03-27
	- [-] Each new server added to the server list should be considered as a new thread **[server thread]**.
		- [x] Modified: Each new server adds itself to the load-balancer. ✅ 2025-03-27
			- [ ] Run check
	- [-] The hashed output should send the client request to that server thread.
		- [x] The hashed output should be used to select the backend server ✅ 2025-03-27
		- [x] send the client jar file to the backend server for processing. ✅ 2025-03-27
		- [ ] Run check
- [-] Allow clients to access the server. ✅ 2025-03-27
	- [x] Allow clients to access the backend servers. ✅ 2025-03-28
	- [x] Allow clients to send files to the backend servers ✅ 2025-03-28
	- [ ] Run check
- [ ] Backend Servers should run the jar files in a protected environment.
	- [x] Client receiving output directly from the backend server. ✅ 2025-03-28
		- [ ] Run check
- [-] A new thread is being generated for each server.
	- [x] Client being run as thread in the backends. ✅ 2025-03-28
	- [ ] run check
- [ ] Make a GUI
	- [ ] show the no. of servers active. **[as icons of servers]**
	- [ ] Show the load on the servers. **[Basically the no. of requests waiting on that.]**
	- [ ] If the server list increases beyond 25, convert the GUI to a spread sheet.

# Extra

- [f] Implement Dictionary using FlipHash.
