```
obj = test.c
CC	= gcc

file = $(wildcard *.c)
drv_x.o = $(patsubst %.c, %.o, $(file))

$(obj):$(obj_o)
	$(CC) -o $@ $^  
$(obj_o):$(file)
	$(CC) -c $^ 
	
all:$(obj)

clean:
	rm *.o
```