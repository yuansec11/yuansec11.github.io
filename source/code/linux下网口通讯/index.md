tcp客户端
```
#include <stdio.h>
#include <sys/socket.h>
#include <netinet/in.h>
#include <arpa/inet.h>

int main(int argc,char *argv[])
{
	int sock;
	struct sockaddr_in aa;
	
	sock = socket(AF_INET, SOCK_STREAM, 0);
    if(sock == -1)
    {
		printf("sock error");
		return -1;
	}
	
	aa.sin_family = AF_INET;
	aa.sin_port = htons(502);
	aa.sin_addr.s_addr = inet_addr("192.168.0.13");
	
	
	if(connect(sock, (struct sockaddr *)&aa, sizeof(aa)) < 0)
	{
		printf("connect error");
		return -1;
	}
	printf("connect with destination host...\n");
	
	while(1)
	{
    	printf("input: ");        
    	char buff[128] = {0};        
    	fgets(buff, 127, stdin);        
    	if(strncmp(buff, "end", 3) == 0)         
		{            
			break;        
		} 
        send(sock, buff, strlen(buff) - 1, 0);
        
        memset(buff, 0, 128);        
        recv(sock, buff, 127, 0);//buff又拿来接收数据  
        printf("%s\n", buff);		
	}
	
	return 0;

}
```

tcp服务器
```
#include <stdio.h>  
#include<unistd.h>  
#include<sys/socket.h>     
#include<netinet/in.h>    
#include<arpa/inet.h>      
#include<stdio.h>  
#include<string.h>  
  
  
int main(int argc,char*argv[])  
{  
    int listenfd,connfd;  
    struct sockaddr_in cliaddr,servaddr;  
   
  
    listenfd = socket(AF_INET,SOCK_STREAM,0);  
  
    bzero(&servaddr,sizeof(servaddr));  
    servaddr.sin_family = AF_INET;  
    servaddr.sin_addr.s_addr = htonl(INADDR_ANY);  
    servaddr.sin_port = htons(502);  
      
    bind(listenfd,(struct sockaddr*)&servaddr,sizeof(servaddr));  
      
    listen(listenfd,5);  
    sleep(60); //将这个注释，会出现另一种情况哟~~    
    while(1)  
    {  
 
    }  
    return 0;  
}
```