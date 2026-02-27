---
title: "LAN-Based Shared Todo Manager"
date: 2025-01-10T11:30:00+05:45
slug: lan-todo-manager
category: projects
summary: "Real-time collaborative task manager using TCP socket programming in C++"
description: "A network programming project implementing a client-server task management system with priority queues, demonstrating low-level socket communication and concurrent client handling."
cover:
  image:
  alt:
  caption:
  relative: true
showtoc: true
draft: false
---

# LAN-Based Shared Todo Manager with Priority Queues

A real-time, collaborative task management system built from scratch using C++ socket programming. This project demonstrates fundamental network programming concepts by implementing a TCP-based client-server architecture where multiple users can manage shared tasks with priority levels across a Local Area Network.

## Overview

Modern collaboration tools often abstract away the underlying network mechanisms. This project takes a lower-level approach, implementing direct TCP socket communication to create a functional todo list manager that:

- Handles multiple concurrent client connections
- Maintains a shared task list across all connected users
- Implements priority-based task organization
- Provides real-time updates to all clients
- Demonstrates core network programming principles

This is an excellent educational project for understanding how networked applications work at the socket level.

---

## Key Features

### 1. **Real-Time TCP Communication**

Direct implementation of Berkeley/POSIX socket APIs:

- Server listens on a specified port
- Accepts multiple client connections
- Maintains persistent connections for real-time updates
- Handles graceful disconnection and reconnection

### 2. **Multi-Client Architecture**

Concurrent client handling using:

- Thread-based approach for each client connection
- Shared task storage accessible by all clients
- Thread-safe operations on shared data structures
- Synchronization mechanisms to prevent race conditions

### 3. **Priority-Based Task Management**

Implements priority queue data structure:

- Tasks organized by priority levels (High, Medium, Low)
- Due date tracking for deadline management
- Automatic sorting by priority and date
- Status tracking (Pending, Completed)

### 4. **Command-Line Interface**

Clean terminal-based interaction:

- User authentication with usernames
- Intuitive command system
- Clear task display and formatting
- Real-time feedback on operations

---

## Technical Architecture

### Server Component (`server.cpp`)

**Responsibilities:**
- Listen for incoming client connections
- Spawn threads for each connected client
- Manage shared task data structure
- Broadcast task updates to all clients
- Handle client disconnections gracefully

**Key Implementation Details:**
```cpp
// Server socket setup
int server_fd = socket(AF_INET, SOCK_STREAM, 0);
bind(server_fd, (struct sockaddr*)&address, sizeof(address));
listen(server_fd, MAX_CLIENTS);

// Accept clients in loop
while (true) {
    int client_socket = accept(server_fd, ...);
    pthread_create(&thread_id, NULL, handle_client, &client_socket);
}
```

### Client Component (`client.cpp`)

**Responsibilities:**
- Connect to server
- Send user commands to server
- Receive and display task updates
- Handle user input and command parsing

**Key Implementation Details:**
```cpp
// Client connection
int sock = socket(AF_INET, SOCK_STREAM, 0);
connect(sock, (struct sockaddr*)&serv_addr, sizeof(serv_addr));

// Separate threads for sending and receiving
pthread_create(&send_thread, NULL, send_messages, &sock);
pthread_create(&recv_thread, NULL, receive_messages, &sock);
```

---

## Supported Commands

### `ADD priority|YYYY-MM-DD|description`

Adds a new task with specified parameters.

**Example:**
```
> ADD high|2026-03-01|Complete network programming assignment
Task added successfully!
```

**Parameters:**
- `priority`: high, medium, or low
- `YYYY-MM-DD`: Due date in ISO format
- `description`: Task details (space-separated)

### `LIST`

Displays all active (pending) tasks sorted by priority.

**Example Output:**
```
=== Current Tasks ===
[1] [HIGH] 2026-03-01: Complete network programming assignment
[2] [MEDIUM] 2026-03-05: Review C++ socket documentation
[3] [LOW] 2026-03-10: Refactor client connection code
```

### `LIST COMPLETED`

Shows all completed tasks.

### `LIST ALL`

Displays both pending and completed tasks.

### `MARK <index>`

Marks a task as completed by its index number.

**Example:**
```
> MARK 1
Task #1 marked as completed.
```

### `exit`

Disconnects from server and exits the client application.

---

## Technology Stack

### Programming Language
- **C++**: Core application logic
- **Standard C++11**: Modern C++ features

### Networking
- **POSIX Sockets (Berkeley Sockets)**: Cross-platform socket API
- **TCP/IP Protocol**: Reliable connection-oriented communication

### Concurrency
- **POSIX Threads (pthread)**: Multi-threading support
- **Mutex Locks**: Thread synchronization

### Platform Support
- **Linux/UNIX**: Primary development platform
- **macOS**: POSIX-compliant support
- **Windows**: With WSL (Windows Subsystem for Linux) or MinGW

---

## Installation & Usage

### Prerequisites

- C++ compiler (g++ recommended)
- UNIX-based system or WSL on Windows
- Basic terminal/console access

### Compilation

**For Linux/macOS:**
```bash
# Clone the repository
git clone https://github.com/rishav-dahal/LAN-TODO.git
cd LAN-TODO

# Compile server
g++ server.cpp -o server -pthread

# Compile client
g++ client.cpp -o client -pthread
```

**For Windows:**
```bash
# Using MinGW or WSL
g++ -std=c++11 server.cpp -o server.exe -lws2_32
g++ -std=c++11 client.cpp -o client.exe -lws2_32
```

### Running the Application

**Step 1: Start the Server**
```bash
./server
# Server output:
# [SERVER] Listening on port 8080...
# [SERVER] Waiting for connections...
```

**Step 2: Connect Clients (in separate terminals)**
```bash
./client
# Connected to server.
# Enter your username: alice
# 
# Commands:
#  - ADD priority|YYYY-MM-DD|description
#  - LIST
#  - LIST COMPLETED
#  - LIST ALL
#  - MARK <index>
#  - exit
#
# > Welcome, alice!
```

**Step 3: Interact with Task Manager**
```bash
> ADD high|2026-03-15|Finish project documentation
> LIST
> MARK 1
> exit
```

---

## Network Programming Concepts Demonstrated

### 1. **Socket Creation and Configuration**

Understanding file descriptors, address families (AF_INET), and socket types (SOCK_STREAM for TCP).

### 2. **Binding and Listening**

Server binds to a specific port and listens for incoming connections.

### 3. **Connection Establishment**

Three-way TCP handshake managed by operating system, application handles accept().

### 4. **Concurrent Client Handling**

Using threads to handle multiple clients simultaneously without blocking.

### 5. **Data Serialization**

Converting data structures to byte streams for network transmission.

### 6. **Error Handling**

Proper handling of network errors, timeouts, and disconnections.

### 7. **Resource Management**

Closing sockets, cleaning up threads, releasing resources properly.

---

## Use Cases

### Educational Projects

- Learn socket programming fundamentals
- Understand client-server architecture
- Practice multi-threaded programming
- Study network protocol implementation

### Team Collaboration

- Small team task tracking on local network
- Lab or office environment task management
- Real-time collaboration without internet dependency

### Prototyping

- Foundation for building networked applications
- Testing ground for network security concepts
- Base for implementing custom protocols

---

## Challenges & Solutions

### Challenge 1: Thread Synchronization

**Problem**: Multiple clients accessing shared task list simultaneously.

**Solution**:
- Implemented mutex locks around critical sections
- Used RAII pattern for automatic lock management
- Designed lock-free read operations where possible

### Challenge 2: Client Disconnection Handling

**Problem**: Server crashes when client disconnects unexpectedly.

**Solution**:
- Added signal handling for broken pipe (SIGPIPE)
- Implemented heartbeat mechanism
- Graceful cleanup of disconnected client resources

### Challenge 3: Input Buffer Management

**Problem**: Reading variable-length messages from TCP stream.

**Solution**:
- Implemented message framing with delimiters
- Used circular buffers for efficient memory use
- Added message validation before processing

---

## Project Contributors

Developed collaboratively by:

- **Rishav Dahal** - Server architecture, network protocol design
- **Bishnu Timilsena** - Client implementation, command parsing
- **Madhav** - Testing, documentation, bug fixes

---

## Future Enhancements

- **Persistent Storage**: Save tasks to database or file
- **User Authentication**: Password-protected user accounts
- **Encryption**: TLS/SSL for secure communication
- **GUI Client**: Graphical interface using Qt or similar
- **Task Notifications**: Reminders for approaching deadlines
- **Task Assignment**: Assign specific tasks to specific users
- **Web Interface**: Browser-based client access
- **Mobile App**: Android/iOS client applications

---

## Technical Learnings

This project provided deep understanding of:

- Low-level socket programming in C++
- TCP/IP protocol mechanics
- Multi-threaded server design patterns
- Network byte ordering (endianness)
- Concurrency and synchronization
- Command-line tool development
- Cross-platform compatibility considerations

---

## Conclusion

The LAN-Based Shared Todo Manager demonstrates core network programming principles through a practical application. By implementing TCP socket communication from scratch in C++, this project provides valuable insights into how networked applications work at the fundamental level.

While modern applications often use higher-level frameworks, understanding these low-level concepts is crucial for any serious software engineer working on networked systems, distributed applications, or performance-critical software.

**GitHub Repository**: [LAN-TODO](https://github.com/rishav-dahal/LAN-TODO)

**Documentation**: Includes project proposal, final report, and presentation materials

**Topics**: Network Programming, C++, Socket Programming, TCP/IP, Multi-threading
