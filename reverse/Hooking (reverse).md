#reverse #hooking
# Hooking under Linux
Mostly used for [[Reverse]], but also for [[Debugging]]
[[LD_PRELOAD]]
## Hooking dynamically resolved functions
    
```c
#define _GNU_SOURCE
#include <stdio.h>
#include <fcntl.h>
#include <dlfcn.h>

int strncmp(const char *s1, const char *s2, size_t n) {
	int (*original_func)(const char *s1, const char *s2, size_t n);
	original_func = dlsym(RTLD_NEXT, "strncmp");
	printf("Comparing %ld chars of %s and %s\n", n, s1, s2);
	return original_func(s1, s2, n);
}
```

```sh
# Compile it as a dynamic library (.so)
gcc -shared -fPIC libhook.c -o hook.so -ldl
# Make it preload by the linker
LD_PRELOAD=./hook.so ./mybin
```
    
### Tips and tricks 
1. Play with the entrypoint 
Defining a function with the `constructor` attribute will define it as the entrypoint and will execute it before anything else (constructors are B A E). This will help us hook internal functions.
    
> A constructor function can also access `argv` and `envp`

📔 This can be particularly useful if you want to access/change the arguments of a program.

```c
//static char *user_input = {0};
__attribute__((constructor))
static void ctor(int argc, char **argv, char **env) {
	for (int i=0; i<argc, i++) {
	  printf("%d) %s\n", i, argv[i]);
  }
  //if (argc > 1) user_input = argv[1];
}
```

2. Check on the callee address to prevent hooking everything

```c
static void *text_start = NULL;
static void *text_end = NULL;

void get_text_addr(pid_t pid) {
	char maps[MAPS_LEN] = {0};
	snprintf(maps, MAPS_LEN, "/proc/%d/maps", pid);
	FILE* f = fopen(maps, "r");
	fscanf(f, "%p-%p", &text_start, &text_end);
	fclose(f);
}

static inline int called_from_target(void* addr) {
	return (addr > text_start && addr < text_end);
}
```
