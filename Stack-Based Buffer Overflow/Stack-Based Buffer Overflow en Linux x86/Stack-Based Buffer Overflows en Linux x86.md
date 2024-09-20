## Vulnerable Program

Ahora estamos escribiendo un programa C simple llamado ``bow.c'`` con una función vulnerable llamada 'strcpy()'.

#### Bow.c

Code: c

```c
#include <stdlib.h>
#include <stdio.h>
#include <string.h>

int bowfunc(char *string) {

	char buffer[1024];
	strcpy(buffer, string);
	return 1;
}

int main(int argc, char *argv[]) {

	bowfunc(argv[1]);
	printf("Done.\n");
	return 1;
}
```