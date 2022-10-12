实现三台设备通讯，1号设备发送消息给2号，2号设备将消息转发给3号  
1号设备  
```
#include<stdio.h>
#include<errno.h>
#include<sys/socket.h>
#include<unistd.h>
#include<netinet/in.h>
#include <arpa/inet.h>
#include <string.h>

int main()
{
	int fd = socket(AF_INET, SOCK_DGRAM, 0);
	if(fd == -1)
	{
		perror("socket");
		return 0;
	}
	struct sockaddr_in cli_addr;	
	cli_addr.sin_family = AF_INET;
	cli_addr.sin_port = htons(502);
	inet_pton(AF_INET, "192.168.0.10", &cli_addr.sin_addr.s_addr);
	
	while(1)
	{
		char buf[BUFSIZ];
		printf("please input message:");
		fgets(buf, 1024, stdin);
		sendto(fd, buf, strlen(buf) - 1, 0, (struct sockaddr *)&cli_addr, sizeof(cli_addr));
		if(strcmp(buf, "quit\n") == 0)
		{
			printf("quit\n");
			break;
		}
		
		sleep(1);
	}
	close(fd);
}
```

2号设备
```
#include<stdio.h>
#include<errno.h>
#include<sys/socket.h>
#include<unistd.h>
#include<netinet/in.h>
#include <arpa/inet.h>
#include <string.h>
#include <pthread.h>


struct message
{
	int i;
	int flag;
	char data[1024];
};


void *udp_send(struct message *udp_rev)
{
	printf("com udp_send\n");
	int i;
	char buf[1024];

	/********************************/
	//初始化udp客户端
	int fd = socket(AF_INET, SOCK_DGRAM, 0);
	if(fd == -1)
	{
		perror("socket");
		return 0;
	}
	struct sockaddr_in cli_addr;	
	cli_addr.sin_family = AF_INET;
	cli_addr.sin_port = htons(1000);
	inet_pton(AF_INET, "192.168.0.13", &cli_addr.sin_addr.s_addr);

	if(connect(fd, (struct sockaddr *)&cli_addr, sizeof(cli_addr)) == -1)
	{
		perror("connect");
		return 0;
	}

	while(1)
	{
		if(udp_rev->flag == 2)
		{
			printf("exit udp_send\n");
			pthread_exit(0);
		}			
		if(udp_rev->flag == 1)
		{
			//printf("udp_send:%s\n", udp_rev->data);
			sprintf(buf, "%s\n", udp_rev->data);
			send(fd, buf, strlen(buf), 0);
			udp_rev->flag = 0;
		}
	}
	
}


int main(int argc, char *argv[])
{
	/********************************/
	//初始化udp服务器
	int fd = socket(AF_INET, SOCK_DGRAM, 0);
	if(fd == -1)
	{
		perror("socket");
		return 0;
	}
	struct sockaddr_in ser_addr;	
	ser_addr.sin_family = AF_INET;
	ser_addr.sin_addr.s_addr = INADDR_ANY;
	ser_addr.sin_port = htons(502);
	
	if(bind(fd, (struct sockaddr *)&ser_addr, sizeof(ser_addr)) == -1)
	{
		perror("bind");
		return 0;
	}

	/********************************/
	//创建线程	
	struct message udp_rev;
	pthread_t cli_id;
	pthread_create(&cli_id, NULL, (void *)*udp_send, &udp_rev);	
	

	while(1)
	{
		char buf[1024];
		char ipbuf[16];
		struct sockaddr_in cli_addr;
		socklen_t size = sizeof(cli_addr);
		int len = recvfrom(fd, buf, sizeof(buf), 0, (struct sockaddr *)&cli_addr, &size);		
		printf("message:%s\t", buf);
		if(inet_ntop(AF_INET, &cli_addr.sin_addr.s_addr, ipbuf,sizeof(ipbuf)) > 0)
		{
			printf("client IP = %s\t", ipbuf);
			printf("port = %d\n", ntohs(cli_addr.sin_port));
		}	

		if(strcmp(buf, "quit") == 0)
		{
			udp_rev.flag = 2;
			printf("exit udp_rev\n");
			break;
		}
		strcpy(udp_rev.data, buf);		
		udp_rev.flag = 1;
		memset(buf, 0, strlen(buf));
	}
	
	pthread_join(cli_id, NULL);
	return 0;
}
```

3号设备（UDP网络调试助手，电脑模拟）