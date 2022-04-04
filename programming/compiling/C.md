#c 

Best use with [[Makefile]] 
## Function with struct
```c
typedef struct Thing {
	int a;
	char[1] b;
} Thing;

int main(void) {
	Thing foo;
	&foo->a = 0x45;
	&foo->b = 'a';

	printf("%d %c\n", &foo->a, &foo->b);

	bar(&foo);
	printf("%d %c\n", &foo->a, &foo->b);

	return 0;
}

void bar(Thing *sef) {
	sef->a = 0x10;
}
```

## Read file
```c

```