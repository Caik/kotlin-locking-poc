# Kotlin Locking
---

##

## About

Kotlin Locking is a POC (Proof Of Concept) built on Kotlin that explore different approaches to use locking mechanisms
in distributed systems environments.

The goal is to test and show how different tools like Hazelcast, Redis, Zookeeper and even a relational database can
help us to solve the problem of needing to lock a shared resource between multiple running instances.

##
---

## Overall architecture

![overall_architecture](https://github.com/Caik/go-stream-broadcast/blob/main/img/overall_architecture.png?raw=true)


##
---

## Overall flow

![flow](https://github.com/Caik/go-stream-broadcast/blob/main/img/flow.png?raw=true)


##
---

## Components

There are two components:

- **Broadcaster**
- **Reader**

**Broadcaster** is the service responsible for managing the file upload to multiple targets.

**Reader** is the service responsible for receiving the file and doing something with it. Its simulate a service like a
S3 upload API, YouTube upload API, etc...


##
---

## Running

There is a configured docker-compose environment composed by one instance of **Broadcaster** and two instances of **
Reader**.

**Docker**, **docker-compose** and **make** will be needed to run:

```bash
# running docker environment
make run_docker
```

---

You can also download the already compiled binaries for **
Linux**([broadcaster](https://github.com/Caik/go-stream-broadcast/blob/main/dist/broadcaster)
, [reader](https://github.com/Caik/go-stream-broadcast/blob/main/dist/reader)) and **
Windows**([broadcaster](https://github.com/Caik/go-stream-broadcast/blob/main/dist/broadcaster.exe)
, [reader](https://github.com/Caik/go-stream-broadcast/blob/main/dist/reader.exe)) on the **dist/** directory and run
them.

PS: You may need to give execution permission to the binary after downloading it:

 ```bash
# giving execution permission on linux
chmod +x ./broadcaster ./reader
```

### Running the binaries

 ```bash
# on a terminal
BROADCASTER_HTTP_PORT=7001 BROADCASTER_HOST_LIST='localhost:7001;localhost:7002' ./broadcaster
```

 ```bash
# on a second terminal
READER_HTTP_PORT=7001 ./reader
```

 ```bash
# on a third terminal
READER_HTTP_PORT=7002 ./reader
```

---

If you have **Go** configured on your environment, you can build your own binaries as well:

```bash
# building a MacOS on AMD64 binary
CGO_ENABLED=0 GOOS=darwin GOARCH=amd64 go build -a -ldflags '-extldflags "-static" -s -w' -o ./broadcaster-darwin-amd64 cmd/broadcaster/main.go

CGO_ENABLED=0 GOOS=darwin GOARCH=amd64 go build -a -ldflags '-extldflags "-static" -s -w' -o ./reader-darwin-amd64 cmd/reader/main.go
```

##
---

## Usage

After configuring and starting the **Broadcaster** and also some **Readers**, you can try to upload a file:

 ```bash
# uploading a file using curl
curl -F file=@video_of_4GB.mp4 http://localhost:7000/broadcast
```

You can also use any other tool for uploading the file. The only requirement is that the request must be a multipart
one.


##
---

## Configuration

There are some environment variables that can be used to configure both services:

- **Broadcaster**

| Variable                | Description                                                                                                                                    | Example                                                                     |
|-------------------------|------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------|
| BROADCASTER_HTTP_PORT   | Server's HTTP port                                                                                                                             | BROADCASTER_HTTP_PORT=8080                                                  |
| BROADCASTER_BUFFER_SIZE | Size of the buffer (bytes). It dictates how much memory will be used and how many iterations will be needed between sender/broadcaster/readers | BROADCASTER_BUFFER_SIZE=2048                                                |
| BROADCASTER_HOST_LIST   | Address of all readers that the broadcaster will broadcast the file                                                                            | BROADCASTER_HOST_LIST=reader1.host:8080;reader2.host:8080;reader3.host:8080 |

- **Reader**

| Variable           | Description                                                          | Example                 |
|--------------------|----------------------------------------------------------------------|-------------------------|
| READER_HTTP_PORT   | Server's HTTP port                                                   | READER_HTTP_PORT=8080   |
| READER_BUFFER_SIZE | Size of the buffer (bytes). It dictates how much memory will be used | READER_BUFFER_SIZE=2048 |

---

The environment variables can be configured the way you like best. Take a look at docker-compose configuration for
reference:

- **[docker-compose.yml](https://github.com/Caik/go-stream-broadcast/blob/main/build/docker/docker-compose.yml)**
- **[.env](https://github.com/Caik/go-stream-broadcast/blob/main/build/docker/.env)**

##
---

## Authors

* Carlos Henrique Severino (**carloshenrique.dev@gmail.com**)