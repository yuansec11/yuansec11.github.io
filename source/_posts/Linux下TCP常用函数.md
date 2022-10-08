---
title: Linux下TCP常用函数
date: 2022-09-25 07:38:35
tags:
- linux
- tcp/ip
- socket
- 嵌入式
categories: Linux
---
## IPV4地址转换  
# inet_addr()  
头文件：#include <sys/socket.h>  
定义函数：in_addr_t inet_addr(const char *cp);  
函数说明：将网络地址字符串转换为32位的网络字节顺序。  
返回值：成功则返回对应的网络二进制的数字, 失败返回-1。 
<!--more--> 
# inet_network()
头文件：#include <sys/socket.h>  
定义函数：in_addr_t inet_network(const char *cp);  
函数说明：将点分十进制的字符串转换为32位的主机字节顺序的IP信息。  
返回值：成功则返回对应的网络二进制的数字, 失败返回-1。
# inet_ntoa()
头文件：#include <sys/socket.h>  
定义函数：char* inet_ntoa(struct in_addr in);  
函数说明：32位网络字节顺序的IP信息转换为字符串。  
返回值：该函数返回指向点分开的字符串地址的指针。
# inet_aton()
头文件：#include <sys/socket.h>  
定义函数：int inet_aton(const char* cp, sturct in_addr* inp);  
函数说明：将字符串转换为32位网络字节顺序。  
第一个参数为要转换的字符串。  
第二个参数为转换结果。  
返回值：成功返回0，否则返回非0值。  
## 地址类型转换
# inet_pton()
头文件：#include <sys/socket.h>  
定义函数：int inet_pton(int af, const char* src, void* dst);  
函数说明：转换字符串到网络地址。  
第一个参数：地址协议类型（AF_INET）  
第二个参数：字符串  
第三个参数：转换结果，类型一般为struct in_addr  
返回值：成功返回1，如果函数出错将返回一个负值。
# inet_ntop()
头文件：#include <sys/socket.h>  
定义函数：const char* inet_ntop(int af, const void* src, char* dst, socklen_t cnt);  
函数说明：转换网络二进制结构到ASCII类型的地址。  
第一个参数：地址协议类型（AF_INET）  
第二个参数：类型一般为struct in_addr  
第三个参数：缓冲区，接收转换结果  
第四个参数：缓冲区大小  
返回值：成功返回1，如果函数出错将返回一个负值。 
## 字节顺序转换函数
# ntohl()
头文件：#include <arpa/inet.h>  
定义函数：unsigned long int ntohl(unsigned long int);  
函数说明：将一个无符号长整形数从网络字节顺序转换为主机字节顺序。  
返回值：
# htons()
头文件：#include <arpa/inet.h>  
定义函数：unsigned long int htons(unsigned long int);  
函数说明：将一个无符号长整形数从主机字节顺序转换为网络字节顺序。  
返回值：
# ntohs()
头文件：#include <arpa/inet.h>  
定义函数：unsigned short int ntohl(unsigned short int);  
函数说明：将一个无符号短整形数从网络字节顺序转换为主机字节顺序。  
返回值：
# ntohs()
头文件：#include <arpa/inet.h>  
定义函数：unsigned short int ntohs(unsigned short int);  
函数说明：将一个无符号短整形数从主机字节顺序转换为网络字节顺序。  
返回值：  

**在这几个函数中n代表net网络，h代表host主机，l代表long长整型，s代表short短整型。**

## socket编程
# socket()
头文件：#include <sys/socket.h>  
定义函数：int socket(int af, int type, int protocol);  
函数说明：创建套接字。  
第一个参数：地址协议类型（AF_INET）  
第二个参数：sock的类型（SOCK_STREAM）  
第三个参数：标识采用协议簇中的哪一种类型，设置为0，让系统自动选择  
返回值：成功返回文件描述符，失败返回-1。
# bind()
头文件：#include <sys/socket.h>  
定义函数：int bind(int sockfd, const struct sockaddr *my_addr, socklen_t addrlen);  
函数说明：对套接字进行地址和端口的绑定。  
第一个参数：socket()函数创建的文件描述符  
第二个参数：指向一个结构为sockaddr参数的指针，sockaddr中包含了地址、端口和IP地址的信息  
第三个参数：my_addr结构的长度，可以设置成sizeof(struct sockaddr)  
返回值：返回值为0时表示绑定成功，-1表示绑定失败。  
# listen()
头文件：#include <sys/socket.h>  
定义函数：int listen(int sockfd, int backlog);  
函数说明：监听网络。  
第一个参数：socket()函数创建的文件描述符  
第二个参数：请求排队的最大长度  
返回值：成功返回0，失败返回-1。
# connect()
头文件：#include <sys/socket.h>  
定义函数：int connect (int sockfd,struct sockaddr * serv_addr,int addrlen);  
函数说明：与服务器建立连接。  
第一个参数：socket()函数创建的文件描述符  
第二个参数：连接的主机地址（包括ip和端口）  
第三个参数：该地址的长度  
返回值：成功返回0，失败返回-1。  
# accept()
头文件：#include <sys/socket.h>  
定义函数：int accept(int sockfd, struct sockaddr *addr, socklen_t *addrlen);  
函数说明：如果监听到客户端的连接请求，则接受请求。  
第一个参数：socket()函数创建的文件描述符  
第二个参数：struct sockaddr类型的地址空间首地址  
第三个参数：该段地址空间长度  
返回值：成功返回一个新的文件描述符，失败返回-1。
# send()
头文件：#include <sys/socket.h>  
定义函数：int send(int sockfd, const void *buf, size_t len, int flags);  
函数说明：发送数据。  
第一个参数：socket()函数创建的文件描述符  
第二个参数：要发送的数据  
第三个参数：要发送的数据的大小  
第四个参数：参数操作flags  
返回值：成功返回发送数据的大小，失败返回-1。
# recv()
头文件：#include <sys/socket.h>  
定义函数：int recv(int sockfd, const void *buf, size_t len, int flags);  
函数说明：接收数据。  
第一个参数：socket()函数创建的文件描述符  
第二个参数：接收数据缓冲区  
第三个参数：接收数据缓冲区的大小  
第四个参数：参数操作flags  
返回值：成功返回接收数据的大小，失败返回-1。  
# close()
头文件：#include <unistd.h>  
定义函数：int close(int sockfd);  
函数说明：关闭socket对象。  
返回值：成功返回0，失败返回-1。
## socket多路复用
# select()
头文件：#include <sys/select.h>  
定义函数：int select(int nfds, fd_set *readfds, fd_set *writefds,fd_set *exceptfds, struct timeval *timeout);
函数说明：从文件描述符获取状态变化。  
第一个参数：监视对象文件描述符数量  
第二个参数：将所有关注“是否存在待读取数据”的文件描述符注册到fd_set变量，并传递其地址值  
第二个参数：将所有关注“是否可传输无阻塞数据”的文件描述符注册到fd_set变量，并传递其地址值  
第四个参数：将所有关注“是否发生异常”的文件描述符注册到fd_set变量，并传递其地址值  
第五个参数：调用select后，为防止陷入无限阻塞状态，传递超时信息  
返回值：错误返回-1，超时返回0。当关注的事件返回时，返回大于0的值，该值是发生事件的文件描述符数。  
# FD_ZERO()
头文件：#include <unistd.h>  
定义函数：FD_ZERO(fd_set* fdset);  
函数说明：将fd_set变量的所有位初始化为0。
# FD_SET() 
头文件：#include <unistd.h>  
定义函数：FD_SET(int fd, fd_set* fdset);  
函数说明：在参数fd_set指向的变量中注册文件描述符fd的信息。
# FD_CLR()
头文件：#include <unistd.h>  
定义函数：FD_CLR(int fd, fd_set* fdset);  
函数说明：参数fd_set指向的变量中清除文件描述符fd的信息。
# FD_ISSET()
头文件：#include <unistd.h>  
定义函数：FD_ISSET(int fd, fd_set* fdset);  
函数说明：若参数fd_set指向的变量中包含文件描述符fd的信息，则返回真。
## socket文件属性
# getsockopt()
头文件：#include <sys/socket.h>  
定义函数：int getsockopt(int sockfd, int level, int optname, void *optval, socklen_t *optlen);
函数说明：获取某个套接字的属性。
第一个参数：socket()函数创建的文件描述符  
第二个参数：选项所在的协议层  
第三个参数：需要访问的选项名  
第四个参数：指向返回选项值的地址  
第五个参数：作为入口参数时，选项值的最大长度。作为出口参数时，选项值的实际长度  
返回值：成功返回0。失败返回-1
# setsockopt()
头文件：#include <sys/socket.h>  
定义函数：int setsockopt(int sockfd, int level, int optname, void *optval, socklen_t *optlen);  
函数说明：获取某个套接字的属性。  
第一个参数：socket()函数创建的文件描述符  
第二个参数：选项所在的协议层  
第三个参数：需要访问的选项名  
第四个参数：指向包含新选项值的地址  
第五个参数：现选项的长度  
返回值：成功返回0。失败返回-1
## 域名和IP信息解析
# gethostbyname()
头文件：#include <sys/socket.h>  
定义函数：struct hostent *gethostbyname(const char *name);  
函数说明：用域名或主机名获取IP地址。  
返回值：成功返回一个hostent的结构的指针，失败返回NULL
# gethostbyaddr()
头文件：#include<netdb.h>  
定义函数：struct hostent * gethostbyaddr(const char * addr, socklen_t len, int family);  
函数说明：通过一个IPv4的地址来获取主机信息。  
第一个参数：指向一个数组的指针，该数组含有一个主机地址  
第二个参数：地址长度  
第三个参数：地址类型  
返回值：成功返回一个hostent的结构的指针，失败返回0
# getaddrinfo()
头文件：#include<netdb.h>  
定义函数：int getaddrinfo(const char *restrict nodename,
                       const char *restrict servname,
                       const struct addrinfo *restrict hints,
                       struct addrinfo **restrict res);
函数说明：主机名到地址解析。  
第一个参数：一个主机名或者地址串(IPv4的点分十进制串或者IPv6的16进制串)  
第二个参数：一个服务名或者10进制端口号数串  
第三个参数：可以是一个空指针，也可以是一个指向某个addrinfo结构的指针  
第四个参数：返回结构链表的指针地址信息  
返回值：成功返回0，失败返回非0