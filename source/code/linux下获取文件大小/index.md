```
#include <sys/stat.h>
#include <stdio.h>
#include <unistd.h>

unsigned long get_file_size(const char *path)
{
	unsigned long filesize = -1;	
	struct stat statbuff;
	if(stat(path, &statbuff) < 0){
		return filesize;
	}else{
		filesize = statbuff.st_size;
	}
	return filesize;
}

int main(int argc, char const *argv[])
{
	struct stat buf;
	stat(argv[1],&buf);
	printf("%s file size = %d\n",argv[1],buf.st_size);
	return 0;
}
```