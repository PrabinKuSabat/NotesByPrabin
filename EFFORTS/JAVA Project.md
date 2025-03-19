# TO-Do

- [ ] Find the xxHash implementation in Java
- [ ] Implement the FlipHash in java
	- [x] Convert the c code to java ✅ 2025-03-19
	- [ ] Make the fliphash write the hashes to a cache file.
- [ ] Make a thread run as a socket.
- [ ] Make FlipHash work on the IP and port numbers.
	- [ ] Each new server added to the server list should be considered as a new thread **[server thread]**.
	- [ ] The hashed output should send the client request to that server thread.
- [ ] Allow clients to access the server.
- [ ] A new thread is being generated for each server.
- [ ] Make a GUI
	- [ ] show the no. of servers active. **[as icons of servers]**
	- [ ] Show the load on the servers. **[Basically the no. of requests waiting on that.]**
	- [ ] If the server list increases beyond 25, convert the GUI to a spread sheet.

# Extra

- [f] Implement Dictionary using FlipHash.
