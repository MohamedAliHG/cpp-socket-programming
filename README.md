# 🔌 cpp-socket-programming

A hands-on **C++ socket programming lab** exploring client/server communication using the POSIX sockets API over TCP and UDP, with progressively advanced exercises: from basic message transfer to concurrent multi-service servers and a raw HTTP client.


---

## 📋 Table of Contents

- [Overview](#overview)
- [Lab Modules](#lab-modules)
- [Tech Stack](#tech-stack)
- [Project Structure](#project-structure)
- [Getting Started](#getting-started)
- [Building the Project](#building-the-project)
- [Running Each Module](#running-each-module)
- [Concepts Covered](#concepts-covered)

---

## Overview

This lab is a series of practical exercises in **low-level network programming in C++** using POSIX sockets. It covers TCP and UDP communication, packet analysis with `tcpdump` / `wireshark`, and the design of robust multi-client server architectures.

**Prerequisites:** C programming, basic knowledge of TCP/UDP.

---

## Lab Modules

### 1. 🌐 Client HTTP en Mode Connecté (TCP HTTP Client)

A raw HTTP client built on TCP sockets that:
- Reads an HTTP request
- Connects to an HTTP server on **port 80** using a TCP socket (`SOCK_STREAM`)
- Sends the raw HTTP request (e.g. `GET / HTTP/1.1`) and displays the server's response
- Packet exchanges (HTTP messages, TCP segments, IP datagrams) are analyzed using `wireshark` / `tcpdump`

Example request format:
```
HEAD / HTTP/1.1
Host: localhost
Accept: */*
<blank line>
```

---

### 2. 📨 Transfert de messages en mode connecté (TCP)

A TCP client/server application where:
- **Station 2 (server)** sends the **current time 60 times** (`Il est hh:mm:ss !`) to Station 1
- **Station 1 (client)** receives and displays the messages
- A `sleep()` delay is added server-side to study TCP segment behavior
- Exercises explore: message counting, TCP segment boundaries vs. `write()` calls, behavior on cable disconnect, and single-client limitation

Communication diagram:
```
Station 1 (Client)          Station 2 (Server)
      | <—— Bonjour ——————————— |
      | <—— Il est hh:mm:ss ! — |
      | <—— Il est hh:mm:ss ! — |
      |          ...             |
      | <—— Au revoir —————————— |
```

---

### 3. 📬 Transfert de messages en mode non connecté (UDP)

A UDP rewrite of the previous exercise using `SOCK_DGRAM`:
- Same scenario: current time sent 60 times
- Uses `sendto()` / `recvfrom()` instead of `send()` / `recv()`
- Compares behavior vs. TCP: no connection establishment, potential message loss, no ordering guarantee
- Explores how to serve multiple clients without a persistent connection

---

### 4. ⚙️ Serveur en mode concurrent (Concurrent Server)

Extends the TCP server to handle **multiple simultaneous clients** using `fork()`:

| File | Description |
|---|---|
| `serveur.cpp` | Mono-protocol, mono-service concurrent server (`fork()` per client) |
| `client.cpp` | Test client to verify parallel request handling |
| `serveur_multi_services.cpp` | Mono-protocol, **multi-service** concurrent server (3 simultaneous services) |
| `client_fichier.cpp` | Client for the file transfer service |
| `client_processus.cpp` | Client for the remote process count service (number of running processes on server) |

The multi-service server offers at minimum:
- The original time-sending service
- A **file transfer** service
- A **remote command execution** service (e.g. returning the number of running processes)

---

## Tech Stack

| Component | Details |
|---|---|
| Language | C++20 |
| Sockets | POSIX Sockets (Linux/Unix) |
| Transport | TCP (`SOCK_STREAM`), UDP (`SOCK_DGRAM`) |
| Concurrency | `fork()` — process-based |
| Build System | CMake 3.29+ |
| Packet Analysis | `tcpdump`, `wireshark` |

---

## Project Structure

```
cpp-socket-programming/
│
├── Client HTTP en Mode connecte/
│   └── client_http.cpp              # Raw HTTP client over TCP (port 80)
│
├── Transfert de messages en mode connecté/
│   ├── client.cpp                   # TCP client (receives time messages)
│   └── serveur.cpp                  # TCP server (sends time 60x)
│
├── Transfert de messages en mode non connecté/
│   ├── client.cpp                   # UDP client
│   └── serveur.cpp                  # UDP server
│
├── Serveur en mode concurrent/
│   ├── serveur.cpp                  # Concurrent server (fork per client)
│   ├── client.cpp                   # Basic test client
│   ├── serveur_multi_services.cpp   # Multi-service concurrent server
│   ├── client_fichier.cpp           # File transfer client
│   └── client_processus.cpp        # Remote process info client
│
├── CMakeLists.txt
└── main.cpp
```

---

## Getting Started

### Prerequisites

- POSIX socket support required
- GCC or Clang with C++20 support
- CMake 3.29+
- `wireshark` or `tcpdump` for packet analysis (optional but recommended)

```bash
g++ --version
cmake --version
```

### Clone

```bash
git clone https://github.com/MohamedAliHG/cpp-socket-programming.git
cd cpp-socket-programming
```

---

## Building the Project

```bash
mkdir build && cd build
cmake ..
make
```

---

## Running Each Module

Each module has a server and a client — open **two separate terminals**.

### TCP Message Transfer

```bash
# Terminal 1 — Server (sends time 60 times)
./serveur_tcp

# Terminal 2 — Client (receives and displays)
./client_tcp
```

### UDP Message Transfer

```bash
# Terminal 1
./serveur_udp

# Terminal 2
./client_udp
```

### Concurrent Server

```bash
# Terminal 1 — Start concurrent server
./serveur_concurrent

# Terminal 2, 3, ... — Launch multiple clients simultaneously
./client
```

### HTTP Client

```bash
# Connects to HTTP server on port 80, reads request from stdin
./client_http
```

Type your HTTP request manually, e.g.:
```
GET / HTTP/1.1
Host: localhost
Accept: */*

```

---

## Concepts Covered

- **POSIX Socket API** — `socket()`, `bind()`, `listen()`, `accept()`, `connect()`, `send()`, `recv()`, `sendto()`, `recvfrom()`, `close()`
- **TCP vs UDP** — reliable ordered stream vs. connectionless datagram, message boundaries
- **HTTP over raw TCP** — manual construction of HTTP/1.1 requests, reading raw responses
- **Packet Analysis** — observing TCP 3-way handshake, segment coalescing, and connection teardown with `wireshark` / `tcpdump`
- **Connection backlog** — behavior when the pending connection queue is full
- **Concurrent Servers** — handling multiple clients in parallel using `fork()`
- **Multi-service Servers** — routing clients to different services within one server
- **File Transfer over Sockets** — reading and sending file data over a TCP connection
- **Remote Command Execution** — executing server-side commands and returning results to the client

---

## Author

**Mohamed Ali HG** — [@MohamedAliHG](https://github.com/MohamedAliHG)

---

*Built in C++20 using raw POSIX sockets 
