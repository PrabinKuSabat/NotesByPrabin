# TO-Do

- [x] Find the xxHash implementation in Java ✅ 2025-03-27
- [x] Implement the FlipHash in java ✅ 2025-03-27
	- [x] Convert the c code to java ✅ 2025-03-19
	- [ ] Make the fliphash write the hashes to a cache file.
- [ ] Make a thread run as a socket.
- [x] Make FlipHash work on the IP and port numbers. ✅ 2025-03-27
	- [ ] Each new server added to the server list should be considered as a new thread **[server thread]**.
	- [ ] The hashed output should send the client request to that server thread.
- [x] Allow clients to access the server. ✅ 2025-03-27
- [ ] A new thread is being generated for each server.
- [ ] Make a GUI
	- [ ] show the no. of servers active. **[as icons of servers]**
	- [ ] Show the load on the servers. **[Basically the no. of requests waiting on that.]**
	- [ ] If the server list increases beyond 25, convert the GUI to a spread sheet.

# Extra

- [f] Implement Dictionary using FlipHash.
