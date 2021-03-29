

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





