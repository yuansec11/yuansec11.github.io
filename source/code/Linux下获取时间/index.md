获取当前时间
```
#include <stdio.h>
#include <time.h>
#include <sys/time.h>

int main()
{

        time_t  now;
        struct  tm  *w;

//精确到毫秒        
#if 1        
        struct timeval tms;
        gettimeofday( &tms, NULL );
#endif        
        
        time(&now);
        w=localtime(&now);
        printf("%04d/%02d/%02d\n%02d:%02d:%02d\n",w->tm_year+1900,
                w->tm_mon+1,w->tm_mday,w->tm_hour,w->tm_min,w->tm_sec);


#if 1        
	printf("%02d:%02d:%02d:%ld\n",w->tm_hour,w->tm_min,w->tm_sec,tms.tv_usec); 
#endif 


        return 0;
}

```

获取时间戳
```
#include <stdio.h>        // for printf()
#include <sys/time.h>    // for gettimeofday()
#include <unistd.h>        // for sleep()

int main()
{
    struct timeval start, end;
    gettimeofday( &start, NULL );
    printf("start : %d.%d\n", start.tv_sec, start.tv_usec);
    sleep(1);
    gettimeofday( &end, NULL );
    printf("end   : %d.%d\n", end.tv_sec, end.tv_usec);

    return 0;
}
```