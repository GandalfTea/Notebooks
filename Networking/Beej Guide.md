

## 3.1 IP Addresses, ver 4 and 6

 
IPv4 : 127.0.0.1 //loopback address
IPv6 : 2001:0DB8:C9D2:0012:0000:0000:0000:0051
       You can remove the zeros : 2001:db8:c9d2:12::51
       ::1 // loopback address
       Full address is : 0000:0000:0000:0000:0000:0000:0000:0001

You can transform an IPv4 into an IPv6 by writing it as:
	::ffff:192.0.2.33


### 3.1.1 Subnets


The first three bytes of an IPv4 are the network and the last byte is a host:
	192.0.2.12
	192.0.2.0  - network
	       .12 - host

The network is constructed by something called a _netmask_. You bitwise AND this with the OP address to get the network number out of it. The netmask usually looks something like : 255.255.255.0

Example:

netmask : 255.255.255.0
IP : 192.0.2.12
Network = 192.0.2.12 AND 255.255.255.0 = 192.0.2.0

The mask can be any number of bits. To show the number, the notation is:
192.0.2.12/30 			- 30 bits
2001:db8::/32			- 34 bits
2001:db8:5413:4028::9db9/64	- 64 bits
	      

### 3.1.2 Port Numbers

Besides the IP, there is another address used by TCP and UDP called the _port number_. It's a 16 bit number that differentiates between computers on the same IP address. For example, HTTP is port 80, telnet is port 23, SMTP is port 25, DOOM is port 666. Ports under 1024 are special and require special OS privilages.

## 3.2 Byte Order

There are two types of computer architecture:
	* Big Endian
	* Little Endian

They store bytes differently:
Storing the hex : b34f
Big Endian      : b3, 4f
Little Endian   : 4f 3b

The _Network Byte Order_ used by the internet is big endian. A computer holds this information in _Host Byte Order_, which can be both. 

In order for the Network Byte Order to be transformed into whatever Host Byte Order is and vice versa, you must assume that the byte order isn't right and run a function o correct it.

There are two types of numbers you can convert:
	* short (2 bytes) + unsigned version
	* long  (4 bytes) + unsigned version

The functions to convert are the following:
	* htons()  - host to network short
	* htonl()  - host to network long
	* ntohs()  - network to host short
	* ntohl()  - network to host long


## 3.3 structs

In a UNIX system, a socket is granted a descriptor just like a normal file descriptor.

A socket descriptor is an `int`.

The struct `addrinfo` is used  to prep the socket address structures for subsequent use. It is also used in host and service name lookups.

```c
struct addrinfo {
	int		ai_flags; 	// AI_PASSIVE, AI_CANONNAME, etc.
	int 		ai_family;	// AF_INET, AF_INET6, AF_UNSPEC
	int 		ai_socktype;	// SOCK_STREAM (TCP), SOCK_DGRAM (UDP)
	int 		ai_protocol;	// 0 for 'any'
	size_t		ai_addrlen;	// size of ai_addr in bytes
	struct sockaddr *ai_addr;	// struct sockaddr_in or _in6
	char		*ai_canonname;	// full canonical hostname

	struct addrinfo *ai_next;	// next node in the linked list
};
```

In the `ai_family` you can force it to use different IP versions or leave it as _AF_UNSPEC_ to use whatever.

The `ai_addr` field in the struct `addrinfo` is a pointer to a struct `sockaddr`.

Note that this is a linked list, `ai_next` points to the next item.

Most of the time you will not have to write the structs yourself, as a call to the function `getaddrinfo()` will fill them out for you. You will need to look inside to get values.

```c
struct sockaddr {
	unsigned short	sa_family;	// address family, AF_xxx
	char		sa_data[14];	// 14 bytes of protocol address;
};
```

`sa_family` describes the version of IP.
`sa_data` contains a destination address and port number for the socket.

To deal with `sockaddr` programmers creates a parallel structure `sockaddr_in` (_in_ for _Internet_) to be used with IPv4.

A pointer to a `sokaddr_in` can be cast to a pointer to `sockaddr` and vice versa. This is used with `connect()` that requires a `sockaddr*`.

```c
// IPv4 only
// struct sockaddr_in6 for OPv6

struct sockaddr_in {
	short int	   sin_family; 	// AF_INET
	unsigned short int sin_port;
	struct in_addr	   sin_addr;	// Interet address
	unsigned chat	   sin_zero[8]; // Padding to make it same size as sockaddr. This should be done with memset()
};
```

The `sin_family` corresponds to a `sa_family` in a `sockaddr` and should be set to _AF_INET_. `sin_port` must be in _Network Byte Order_ (using `htons()`).

```c
// IPv4 only

struct in_addr {
	uint32_t s_addr; // 32 bit int (4 bytes)
};
```

If you have declared `ina` to be of type `sockaddr_in`, than `ina.sin_addr.s_addr` references the 4-byte IP address in _Network Byte Order_.



Similar structs exist for IPv6:

```c
struct sockaddr_in6 {
	u_int16_t	sin6_family;	// AF_INT6
	u_int16_t	sin6_port;	// port number, Network Byte Order
	u_int16_t	sin6_flowinfo;	// IPv6 flow information
	struct in6_addr	sin6_addr;	// IPv6 address
	u_int16_t	sin6_scope_id;	// Scope ID
};
```
```c
struct in6_addr {
	unsigned char s6_addr[16];  // Ipv6 address
};
```


There is another struct designed to be large enough to hold both IPv4 and IPv6 structures, names `sockaddr_storage`. Sometimes, you don't know in advance what IP version is gonna fill `sockaddr`. You pass it instead to this parallel structure and then cast it to the type you need:

```c
struct sockaddr_storage {
	sa_family_t 	ss_family;	// address family

	// Padding
	char 	    	__ss_pad1[_SS_PAD1SIZE];
	int64_t		__ss_align;
	char		__ss_pad2[_SS_PAD2SIZE];
};
```


## 3.4 IP Addres, Part Deux

There are a lot of functions to manipulate the IP address.

Let's say you have a struct `sockaddr_in ina` and the IP address `10.12.110.57` or `2001:db8:63b3:1::3490` that should be stored into it. You use the function `inet_pton()` converts the IP addressfrom numbers and dots into either struct `in_addr` or a `in6_addr`. It stands for 'presentation to network':

```c
struct sockaddr_in  sa;	 //IPv4
struct sockaddr_in6 sa6; //IPv6

inet_pton(AF_INET,  "10.12.110.57",	     &(sa.sin_addr));	//IPv4
inet_pton(AF_INET6, "2001:db8:63b3:1::3290", &(sa6.sin6_addr)); //IPv6
```

This code has no error checking. The function returns -1 on error or 0 orf the address is not ok. Always check that the result is greater than 0.

The revers is also possible with the function `inet_ntop()`, meaning 'network to presentation' :

```c
// IPv4

char ip4[INET_ADDRSTRLEN];	// space for the IPv4  string
struct sockaddr_in sa;		// this should be loaded with something.

inet_ntop(AF_INET, &(sa.sin_addr), ip4, INET_ADDRSTRLEN);

printf("The IPv4 address is : %s\n", ip4);
```

```
//IPv6

char ip6[INET6_ADDRSTRLEN];
struct sockaddr_in6 sa6;

inet_ntop(AF_INET6, &(sa6.sin6_addr), ip6, INET6_ADDRSTRLEN);

printf("The IPv6 address is : %s\n", ip6);
```

The functions only work woth numeric IP addresses, they don't do any DNS lookup on a hostname.


### 3.4.1 Private (Disconnected) Networks

People usually have a firewall at home that converts internal IPs (private) to external IPs(public). 

Private ones usually look like this: 10.0.0.5.
The firewall does _Network Address Translation_ or NAT to convert from internal to external.


## Chapter 4 : Jumping from IPv4 to IPv6

// TODO



## Chapter 5 : System Calls or Busts


### 5.1 getaddrinfo()

This function sets up the structs for later use. This includes DNS and service name lookups.

```c
#include <sys/types.h>
#include <sys/socket.h>
#include <netdb.h>

int getaddrinfo( const char *node,		// www.example.com or IP
		 const char *service,		// http or port number
		 const struct addrinfo *hints,
		 struct addrinfo **res);
```
It returns a pointer to a linked-list, `res`, of results.
The `node` param. is the host name to connect to, or IP address.
The `service` param. can be a port number, like 80, or the name of a particular service like "http", "ftp", "telnet", "smtp", etc.
The `hints` param points to a struct `addrinfo` that you've already filled with relevant information.

Example for a server that wants to listen on the host's IP address, port 3490. Note that is does not actually listen, just sets up structures for later use.

```c
int status;
struct addrinfo hints;
struct addrinfo *serviceinfo;

memset(&hints, 0, sizeof hints);	// empty the struct
hints.ai_family = AF_UNSPEC;		// either IP version
hints.ai_socktype = SOCK_STREAM;	// TCP
hints.ai_flags = AI_PASSIVE;		// fill in my IP for me

if ((status = getaddrinfo(NULL, "3490", &hints, &servinfo)) != 0) {
	fprint(stderr, "getaddrinfo error : %s\n", gai_strerror(status));
	exit(1);
}
```
`servinfo` now points to a linked list of 1 or more struct `addrinfo`s.

After using the linked-list, you should free the memory:
```c
freeaddrinfo(servinfo);
```

If you are a client and want to connect to a particular server:
```c
int status;
struct addrinfo hints;
struct addrinfo *servinfo;	// will point to result.

memset(&hints, 9, sizeof hints);
hints.ai_family = AF_UNSPEC;
hints.ai_socktype - SOCK_STREAM;

status = getaddrinfo("www.example.com", "3490", &hints, &servinfo);
```
 
### 5.2 socket()

Brakedown:

```c
#include <sys/types.h>
#include <sys/socket.h>

int socket (int domain, int type, int protocol);
```
`domain` is either IPv4 or IPv6.
`type` is either stream or datagram.
`protocol` is either TCP or UDP.

Domain here is either _PF_INET_ or _PF_INET6_, which is very similar to _AF_INET_ and _AF_INET6_. The formet stands for _Protocol Family_ and the latter stands for _Address Family_. They are so similar they can be used interchangebly. The correct use is _AF_INET_ in the struct `sockaddr_in` and _PF_INET6_ in your call to `socket()`.

What you want to do with the values resulted from `getaddrinfo()` is feed them into the `socket()` like this:
```c
int s;
struct addrinfo hints, *res;

// Do the lookup
// Hints should be filled up

getaddrinfo("www.example.com", "http", &hints, &res);

// Error checking the result should be done here
// You should walk the 'res' linked list looking for valid entries
// instead of just assuming the first one is good.

s = socket(res->ai_family, res->ai_socktypem res->ai_protocol);
```

`socket()` should return a _socket descriptor_ to use further, or -1 on error. It also sets `errno`, a global variable, to the specific error.


### 5.3 bind()


Once you have a socket, you might have to associate it with a port on the local machine. This is currently done by the function `listening()` for incoming connections on a specific port. This is if you are the server. If you are a client, you're only going to `connect()`.

Function:
```c
#include <sys/types.h>
#include <sys/socket.h>

int bind(int sockfd, struct sockaddr *my_addr, int addrlen);
```

`sockfd` is the socket file descriptor returned by `socket()`.
`my_addr` is a pointer to a struct `sockaddr` that contains info about your address, namely, port and IP address.


Example, bind socket to the host of the program, port 3490:

```c
struct addrinfo hints, *res;
int sockfd;

// Load up structs with getaddrinfo()

memset(&hints, 0, sizeof hints);
hints.ai_family = AF_UNSPEC;
hints.ai_socktype = SOCK_STREAM;
hints.ai_flags = AI_PASSIVE;

getaddrinfo(NULL, "3490", &hints, &res);

// Make a socket

sockfd = socket(res->ai_family, res->ai_socktype, res->ai_protocol);

// Bind it

bind(sockfd, res->ai_addr, res->ai_addrlen);
```
By using `AI_PASSIVE` flag, the program binds to the IP the host is running on. If you want a specific IP address, put the IP in the first argument to `getaddrinfo()`. 

`bind()` also returns -1 on error and sets `errno` to the error's value.

When calling this function, do you must choose a port between 1024 - 65535. Somethimes when you try to rereun a server and `bind()` fails, claiming "Address already in use", you can either wait a minute for it to clear or add code to the program allowing it to reuse the port :

```c
int yes=1;

if(setsockopt(listener, SOL_SOCKET, SO_REUSEADDR, &yes, sizeof yes) == -1) {
	perror("setsockopt");
	exit(1);
}
```


### 5.4  connect()

If you are a telnet application, a user will ask you to get a socket file descriptor using the function `socket()`. It also tells you to connect to "10.12.110.57" on port "23". For this, you use the function `connect()`:

```c
#include <sys/types.h>
#include <sys/socket.h>

int connect(int sockfd, struct sockaddr *serv_addr, int addrlen);
```
`sockfd` is the socket file descriptor.
`serv_addr` is a struct `sockaddr` containing the destination port and IP address.
`addrlen` is the length in bytes of the server address structure.

All of this information can is given by a call to `getaddrinfo()`.

An example, connecting to "www.example.com", port 3490:

```c
struct addrinfo hints, *res;
int sockfd;

// Load up structs

memset(&hints, 0, sizeof hints);
hints.ai_family = AF_UNSPEC;
hints.ai_socktype = SOCK_STREAM;

getaddrinfo("www.example.com", "3490", &hints, &res);

// Make a socket

sockfd = socket(res->ai_family, res->ai_socktype, res->ai_protocol);

// Connect

connect(sockfd, res->ai_addr, res->ai_addrlen);
```

The return value from `connect()` is -1 on error, and sets the variable `errno`.

Notice that we did not call `bind()`. We don't care about our local port number, only the remote port. The kernel will choose a local port for us, and the website will automatically get this info from us.


### 5.5 listen()

If you don;t want to connect to a remote host, you can listen for incoming conections and handlem them in some way:

```c
int listen (int sockfd, int backlog);
```
`backlog` is the number of connections allowed on the incoming queue. Incoming connections will wait in this queue until you `accept()` them. Most systems silently limit this number to 20; you can probably get away with setting it to 5 or 10.

`listen()` returns -1 and sets `errno` on error. 

Before we `listen()` we need to `bind()` the server to a specific port. The sequence of functions is :
```c
getaddrinfo();
socket();
bind();
listen()
// accept()
```

### 5.6 accept()


When accepting a connection from the queue, it will return to you a brand new _socket file desctiptor_ to use for this single connection, while the original is still listening for incoming connections. You can use the newly created one to `send()` and `recv()`.

```c
#include <sys/types.h>
#include <sys/socket.h>

int accept(int sockfd, struct sockaddr *addr, socklen_t *addrlen);
```
`sockfd` is the `listen()` socket descriptor.
`addr` will usually point to a local struct `sockaddr_storage`. This is where the info about the incoming connection will go, allowing you to determine which host is calling you from which port.
`addrlen` is a local integer variable set to the size of the `sockaddr_storage` struct before the address is passed to `accept`.

`accept()` returns -1 and sets `errno` in case of error.

Example:
```c
#include <string.h>
#include <sys/types.h>
#include <sys/socker.h>
#include <netdb.h>

#define MYPORT "2490"
#define BACKLOG 10

int main(void) {
	struct sockaddr_storage their_addr;
	socklen_t addr_size;
	struct addrinfo hints, *res;
	int sockfd, new_fd;

	// Error checking goes here.

	// Load up address structs
	memser(&hints, 0, sizeof hints);
	hints.ai_family = AF_UNSPEC;
	hints.ai_socktype = SOCK_STREAM;
	hints.ai_flags = AI_PASSIVE;

	getaddrinfo(NULL, MYPORT, &hints, &res);

	// Make a socket
	sockfd = socket(res->ai_family, res->ai_socktype, res->ai_protocol);
	bind(sockfd, res->ai_addr, res->ai_addrlen);
	listen(sockfd, BACKLOG);

	// Accept connections
	addr_size = sizeof their_addr;
	new_fd = accept(sockfd, (struct sockaddr *)&their_addr, &addr_size);

	// Ready to communicate
	
	.
	.
	.
}
```

### 5.7 send() and recv()

You can use those functions to communicate both over stream sockets and datagram sockets. If you want to use unconnected datagram sockets, you will have to use `sendto()` and `recvfrom()`. 

```c
int send(int sockfd, const void *msg, int len, int flags);
```
`sockfd` is the socket descriptor to send the data to.
`msg` is a pointer to the message data.
`len` length of data in bytes.
`flags` should be set to 0.

Example:
```c
char *msg = "Beej was here!";
int len, bytes_sent;

.
.
.

len = strlen(msg);
bytes_sent = send(sockfd, msg, len, 0);
```

`send()` returns the number of bytes actually sent out. Sometimes this is not all the data. If the value returned does not match the len of the string, it's up to you to send the rest. If the package is less than 1k or so, it will probably manage to send it all.

-1 is returned on error and `errno` is set to the error number.

`recv()` is similar:
```c
int recv(int sockfd, void *buf, int len, int flags);
```
`sockfd` is the socket desc. to recieve from.
`buf` is the buffer to read the information into.
`len` is the maximum length of the buffer.
`flags` can again be 0.

It returns the number of bytes actually read into the buffer, or -1 on error(with `errno`).

This function can also return 0, meaning the remote side has closed the connection with you.

### 5.8 sendto() and recvfrom()

```c
int sendto(int sockfd, const void *msg, int len, unsigned int flags,
	   const struct sockaddr *to, socklen_t tolen);
```

This is almost the same call as `send()` with 2 additional pieces of information.
`to` is a pointer to a struct `sockaddr` (most likely, a `sockaddr_in`, `sockaddr_in6`, or `sockaddr_storage` that is cast) containing the destination IP address and port.
`tolen`, at the base an int, cab simply be the size of `*to` or size of the struct `sockaddr_storage`.

To get the destination address, you can either use `getaddrinfo()` or get it from `recvfrom()`.

`sendto()` returns the number of bytes actually sent, or -1 on error.

`recv()` and `recvfrom()` are also very similar:
```c
int recvfrom(int sockfd, void *buf, int len, unsigned int flags,
	     struct sockaddr *from, int *fromlen);
```


`from` is a pointer to a local struct `sockaddr_storage` filled with the IP nd port of the originating machine.
`fromlen` is a pointer to a local int that should be sizeof `*from` or sizeof `srockaddr_storage`.

When the function returns,`fromlen` will contain the length of the address actually stored in from.

It returns the number of bytes recieved, or -1 on error, with `errno`.

#### Why do we use struct `sockaddr_storage` as the socket type?
Why not `sockaddr_in`? Because we do not want to tie ourselves down to IPv4 or IPv6. We use the generic struct that is big enough for either.

#### Why isn't struct `sockaddr` itself big enough?
We even cast the `sockaddr_storage` into it. It just is not big enough. It is old and problematic to change it at this time.


### 5.9 close() and shutdown()

To close a socket, simply use:
```c
close(sockfd);
```

This will prevent all reads and writes.

In case you need more control over how the connection is closed, like cutting off communication in a certain direction, you use the `shutdown()` function:
```c
int shutdown(int sockfd, int how);
```

You can:
```
how  |  Effect
 0   |  Further recieves are not allowed
 1   |  Further sends are not allowed
 2   |  Further sends and recieves are not allowed (like close())
```

It returns 0 on success and -1 on error (with `errno`).

If you use `shutdown()` on unconnected datagram sockets, it will make te socket unavailable for further `send()` and `recv()` (you can use them if you `connect()`).

It's important to note that `shutdown()` does not actually close the file desctiptor, it just changes usability. You still have to call `close()` to free the socket descriptor.

(On Windows, you should use `closesocket()` instead of `close()`)


### 5.10 getpeername()

This function will tell you who is at the other end of a connected stream stocket.
```c
#include <sys/socket.h>

int getpeername(int sockfd, struct sockaddr *addr, int *addrlen);
```

`sockfd` is the descriptor of the stream socket.
`addr` is a pointer to a struct `sockaddr` (or `sockaddr_in`) that will hold the information about the other side of the connection.
`addrlen` is a pointer to an int, initialized to sizeof `*addr` or sizeof `sockaddr`.

The function returns -1 on error and sets `errno`.

Once you have the address, you can use `inet_ntop()`, `getnameinfo()` or `gethostbyaddr()` to print more info.

### 5.11 gethostname()

This returns the name of the computer that your program is running on. The name can be used by `gethostbyname()` to determine the IP of your local machine.

```c
#include unistd.h>

int gethostname(char *hostname, size_t size);
```
`hostname` is a pointer to an array of chars that will contain the hostname upon the function return.
`size` is the length in bytes of the hostname array.




## Chapter 6 : Client - Server Background


The exchange of information between client and server is summarized in the following diagram:

//TODO: DIAGRAM

Often, there wil only be one server on a machine, therefore it will need to handle multople clients using `fork()`. The basic routine is: a server accepts's a connection, and it `fork()`s a child process to handle it.

### 6.1 A Simple Stream Server

All it does is send a string "Hello, world!" over a stream connection. You need to run the server in one window and run this command in another:
```
$ telnet remotehostname 3490
```
where `remotehostname` is the name of the machine you're on.

```c
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <errno.h>
#include <string.h>
#include <sys/types.h>
#include <sys/socket.h>
#include <netinet/in.h>
#include <netdb.h>
#include <arpa/inet.h>
#include <sys/wait.h>
#include <signal.h>

#define PORT "3490"

#define BACKLOG 10

void sigchld_handler(int s) {

	// waitpid() might overwrite errno, so we save and restore it:
	int saved_errno = errno;

	while(waitpid(-1, NULL, WNOHANG) > 0);

	errno = saved_errno;
}

// get sockaddr, IPv4 or IPv6
void *get_in_addr(struct sockaddr *sa) {
	if (sa->sa_damily == AF_INET){
		return &(((struct sockaddr_in*)sa)->sin_addr);
	}
	
	return &(((struct sockaddr_in6*)sa)->sin6_addr);
}


int main(void) {

	int sockfd, new_fd;	// Listen on sockfd, new connection on new_fd
	struct addrinfo hints, *servinfo, *p;
	struct sockaddr_storage their_addr;
	socklen_t sin_size;
	struct sigaction sa;
	int yes = 1;
	char s[INET_ADDRSTRLEN];
	int rv;

	memset(&hints, 0, sizeof hints);
	hints.ai_family = AF_UNSPEC;
	hints.ai_socktype = SOCK_STREAM;
	hints.ai_flags = AI_PASSIVE;

	if ((rv = getaddrinfo(NULL, PORT, &hints, &servinfo)) != 0) {
		fprint(stderr, "getaddrinfo : %s\n", gai_strerror(rv));
		return 1;
	}

	// loop through all the results and binf to the first we can
	for (p = servinfo; p != NULL; p = p->ai_next) {

		if ((sockfd = socket(p->ai_family, p->ai_socktype,
				p->ai_protocol)) == -1) {
			perror("Server: Socket");
			continue;
		}

		if (setsockopt(sockfd, SOL_SOCKET, SO_REUSEADDR, &yes,
				sizeof(int)) == -1) {
			perror("sersockopt");
			exit(1);
		}

		if (bind(sockfd, p->ai_addr, p->ai_addrlen) == -1) {
			close(sockfd);
			perror("Server: bind");
			continue;
		}
	
		break;
	}	

	freeaddrinfo(servinfo);
	
	if (p == NULL) {
		fprint(stderr, "server: failes to bind\n");
		exit(1);
	}
	
	if (listen(sockfd, BACKLOG) == -1) {
		perror("listen");
		exit(1);
	}

	sa.sa_handler = sigchld_handler;
	sigemptyset(&sa.sa_mask);
	sa.sa_flags = SA_RESTART;
	
	if (sigaction(SIGCHLD, &sa, NULL) == -1) {
		perror("sigaction");
		exit(1);
	}

	printf("Server: waiting for connections...\n");

	while(1) {
		sin_size = sizeof their_addr;
		new_fd = accept(sockfd, (struct sockaddr *)&their_addr, &sin_size);
		if (new_fd == -1) {
			perror("accept");
			continue;
		}

		iner_ntop(their_addr.ss_family, 
			  get_in_addr((struct sockaddr *)&their_addr),
			  s, sizeof s);
		printf("Server: got connection from %s\n", s);

		if (!fork()) {
			close(sockfd);
			if(send(new_fd, "Hello World!", 12, 0) == -1)
				perror("send");
			close(new_fd);
			exit(0);
		}
	
		close(new_fd);
	}
	return 0;
}

```

