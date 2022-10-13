---
title: 简单代码实现MQTT客户端功能
date: 2022-10-13 09:08:28
categories: 嵌入式
tags:
- linux
- MQTT
- wireshark
- tcp/ip
---
MQTT协议的工作流程如下：
<!--more-->    
![](https://s1.ax1x.com/2022/10/13/xaHrx1.png)  
其实很简单，就是通过客户端向服务器发布订阅主题来实现信息的交互。这里我将用简单的代码实现客户端的功能。

# MQTT服务器安装
这里我用的树莓派作为服务器，安装的服务器是mosquitto。  
安装流程很简单
```
sudo apt-get update #获取更新
sudo apt-get install mosquitto #安装mosquitto服务器
mosquitto -v #启动mosquitto服务器
```

依次输入上面代码即可，当然，你也可以安装到其他地方，比如虚拟机，或者电脑都可以，下面这个网址有mosquitto在不同环境的安装包和教程  
[软件下载地址](https://mosquitto.org/download)

# 电脑模拟抓包
服务器安装完成后，需要先分析以下客户端和服务器之间的通讯，先找电脑上装一个MQTT客户端软件，这里我装的是MQTT.fx,安装包网上有很多资源，可以自己去找，接下来打开软件，按照步骤输入以下内容，其中服务器默认端口是1883，所以一定要设置为1883。
![](https://s1.ax1x.com/2022/10/13/xaHyKx.png)  
然后打开wireshark，准备好了之后，点击MQTT.fx软件上的connect开始抓包，不会wireshark软件可参考我的一篇文章
[wireshark抓包](https://www.yuansec.top/2022/10/08/wireshark%E6%8A%93%E5%8C%85/#more)  
通过抓取的包，发现，MQTT协议是基于TCP/IP协议的，客户段连接服务器的动作，首先是tcp三次握手，接着发送连接请求命令。
![](https://s1.ax1x.com/2022/10/13/xaHD2R.png)
继续抓包，新建一个发布，发送给服务器，这里使用的QoS0等级发送，可以看出，发送很简单，一条报文即可，解析出来的数据可以看出，这条报文包含了主题和内容
![](https://s1.ax1x.com/2022/10/13/xaHd54.png)
接着，新建一个订阅，抓取报文
![](https://s1.ax1x.com/2022/10/13/xaH0PJ.png)
最后，再次发布，可以打看到数据发送到了订阅，同时抓取的报文可以看到数据交互过程
![](https://s1.ax1x.com/2022/10/13/xaHaaF.png)
# 代码实现
这里我在虚拟机实现客户端功能，linux环境  
首先按照刚刚的步骤，先进行sock连接，进行tcp的三次握手
```
int connect_sock()
{	
	int sock;
	struct sockaddr_in aa;
	
	sock = socket(AF_INET, SOCK_STREAM, 0);
	if(sock == -1)
    	{
		printf("sock error\n");
		return -1;
	}
	
	aa.sin_family = AF_INET;
	aa.sin_port = htons(1883);
	aa.sin_addr.s_addr = inet_addr("192.168.0.10");
	
	if(connect(sock, (struct sockaddr *)&aa, sizeof(aa)) < 0)
	{
		printf("connect error\n");
		return -1;
	}
	printf("connect with destination host...\n");
	
	return sock;
}
```

接着，MQTT连接请求，这里参照抓包的格式，直接定义好每一个字节
```
int connect_pub(int fd)
{
	char data[1024];
	data[0] = 0x10;
	data[1] = 14;
	data[2] = 0x00;
	data[3] = 0x04;
	data[4] = 'M';
	data[5] = 'Q';
	data[6] = 'T';
	data[7] = 'T';
	
	data[8] = 4;
	data[9] = 2;
	
	data[10] = 0x00;
	data[11] = 0x3c;
	
	data[12] = 0x00;
	data[13] = 0x02;
	
	data[14] = 'f';
	data[15] = 'e';

	int sum = send(fd, data, 16, 0);
	if(sum < 0)
	{
		printf("send error\n");
		return -1;
	}
	printf("connect_pub send sum = %d\n",sum);
	
	return 0;
}
```

接着就是建立订阅连接
```
int connect_sub(int fd, char *topic)
{
	char data[1024];
	int i;
	data[0] = 0x82;
	
	data[2] = 0x00;
	data[3] = 0x03;

	int len = strlen(topic);
	data[4] = len/256;
	data[5] = len%256;
	for(i = 0; i < len; i++)
		data[6 + i] = topic[i];
		
	data[6 + len] = 0x00;
	data[1] = 5 + len;

	int sum = send(fd, data, 7 + len, 0);
	if(sum < 0)
	{
		printf("send error\n");
		return -1;
	}
	printf("connect_sub send sum = %d\n",sum);
	
	return 0;
}
```

建立好订阅连接后，就可以发布内容了，同时创建一个线程，接收服务器发回来的数据
```
int pub(int fd, char *topic, char *str)
{
	char data[1024];
	int i;
	
	data[0] = 0x30;
	
	int t_len = strlen(topic);
	data[2] = t_len/256;
	data[3] = t_len%256;
	
	for(i = 0; i < t_len; i++)
		data[4 + i] = topic[i];
		
	int s_len = strlen(str);
	for(i = 0; i < s_len; i++)
		data[4 + t_len + i] = str[i];
	
	data[1] = 2 + t_len + s_len;	
	
	int sum = send(fd, data, 4 + t_len + s_len, 0);
	if(sum < 0)
	{
		printf("send error\n");
		return -1;
	}
	//printf("pub sum = %d\n",sum);
	
	return 0;
}

void *sub_rev(struct msg *msg_sub)
{
	char data[1024];
	char str[1024];

	while(1)
	{
		int num = recv(msg_sub->fd, data, 1024, 0);
		if(num < 0)
		{
			printf("recv error\n");
			return 0;
		}
		if(num > 0)
		{
			if(data[0] == 0x30)
			{
				int len = data[2]*256 + data[3];
				if(strncmp(data + 4, msg_sub->topic, len) == 0)
				{
					strncpy(str, data + 4 + len, num - 4 - len);
					printf("recv data:%s\n", str);
					memset(data, '\0', strlen(data));
					memset(str, '\0', strlen(str));
				}
				
			}
		}
	}
}
```

# 实现效果
这里主题名固定了，实际应用的话可以自己定义主题，可以看到，效果实现了
![](https://s1.ax1x.com/2022/10/13/xaHBG9.png)
# 完整代码
最后还有一个断开连接的代码，这里我就不去完成了，原理是一样的
```
#include<stdio.h>
#include<sys/socket.h>
#include <netinet/in.h>
#include <arpa/inet.h>
#include <string.h>
#include <pthread.h>
#include <unistd.h>

int connect_sock()
{	
	int sock;
	struct sockaddr_in aa;
	
	sock = socket(AF_INET, SOCK_STREAM, 0);
	if(sock == -1)
    	{
		printf("sock error\n");
		return -1;
	}
	
	aa.sin_family = AF_INET;
	aa.sin_port = htons(1883);
	aa.sin_addr.s_addr = inet_addr("192.168.0.10");
	
	if(connect(sock, (struct sockaddr *)&aa, sizeof(aa)) < 0)
	{
		printf("connect error\n");
		return -1;
	}
	printf("connect with destination host...\n");
	
	return sock;
}

int connect_pub(int fd)
{
	char data[1024];
	data[0] = 0x10;
	data[1] = 14;
	data[2] = 0x00;
	data[3] = 0x04;
	data[4] = 'M';
	data[5] = 'Q';
	data[6] = 'T';
	data[7] = 'T';
	
	data[8] = 4;
	data[9] = 2;
	
	data[10] = 0x00;
	data[11] = 0x3c;
	
	data[12] = 0x00;
	data[13] = 0x02;
	
	data[14] = 'f';
	data[15] = 'e';

	int sum = send(fd, data, 16, 0);
	if(sum < 0)
	{
		printf("send error\n");
		return -1;
	}
	printf("connect_pub send sum = %d\n",sum);
	
	return 0;
}
int connect_sub(int fd, char *topic)
{
	char data[1024];
	int i;
	data[0] = 0x82;
	
	data[2] = 0x00;
	data[3] = 0x03;

	int len = strlen(topic);
	data[4] = len/256;
	data[5] = len%256;
	for(i = 0; i < len; i++)
		data[6 + i] = topic[i];
		
	data[6 + len] = 0x00;
	data[1] = 5 + len;

	int sum = send(fd, data, 7 + len, 0);
	if(sum < 0)
	{
		printf("send error\n");
		return -1;
	}
	printf("connect_sub send sum = %d\n",sum);
	
	return 0;
}
int pub(int fd, char *topic, char *str)
{
	char data[1024];
	int i;
	
	data[0] = 0x30;
	
	int t_len = strlen(topic);
	data[2] = t_len/256;
	data[3] = t_len%256;
	
	for(i = 0; i < t_len; i++)
		data[4 + i] = topic[i];
		
	int s_len = strlen(str);
	for(i = 0; i < s_len; i++)
		data[4 + t_len + i] = str[i];
	
	data[1] = 2 + t_len + s_len;	
	
	int sum = send(fd, data, 4 + t_len + s_len, 0);
	if(sum < 0)
	{
		printf("send error\n");
		return -1;
	}
	//printf("pub sum = %d\n",sum);
	
	return 0;
}

struct msg
{
	int fd;
	char *topic; 
};
void *sub_rev(struct msg *msg_sub)
{
	char data[1024];
	char str[1024];

	while(1)
	{
		int num = recv(msg_sub->fd, data, 1024, 0);
		if(num < 0)
		{
			printf("recv error\n");
			return 0;
		}
		if(num > 0)
		{
			if(data[0] == 0x30)
			{
				int len = data[2]*256 + data[3];
				if(strncmp(data + 4, msg_sub->topic, len) == 0)
				{
					strncpy(str, data + 4 + len, num - 4 - len);
					printf("recv data:%s\n", str);
					memset(data, '\0', strlen(data));
					memset(str, '\0', strlen(str));
				}
				
			}
		}
	}
}

int main()
{
	int sock;	
	char topic[1024] = "test";
	char str[1024];
	
	sock = connect_sock();
	
	if(sock < 0)
	{
		printf("connect_sock error\n");
		return 0;
	}
	if(connect_pub(sock) < 0)
	{
		printf("connect_pub error\n");
		return 0;
	}
	if(connect_sub(sock, topic) < 0)
	{
		printf("connect_sub error\n");
		return 0;
	}
	
	
	pthread_t sub_id;
	struct msg sub_msg;
	sub_msg.fd = sock;
	sub_msg.topic = topic;
	pthread_create(&sub_id, NULL, (void*)*sub_rev, &sub_msg);
	usleep(100 * 1000);
	
	while(1)
	{
		printf("please input message:");
		fgets(str, 1024, stdin);
		pub(sock, topic, str);
		memset(str, '\0', strlen(str));
		usleep(100 * 1000);
	}
	
	
	close(sock);
	
}
```


