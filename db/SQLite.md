#db 

## DB init
```sql
sqlite3 database.db < script.sql
sqlite3 database.db

select * from table;
```

## Custom extension
https://www.sqlite.org/loadext.html
https://www.sqlite.org/appfunc.html

Sample code to create a new SQLite function which returns the first argument times 2:

```c
// gcc -g -fPIC -shared myext.c -o myext.so

#include "sqlite3ext.h"
SQLITE_EXTENSION_INIT1
#include <assert.h>

static void bonk(
    sqlite3_context *context,
    int argc,
    sqlite3_value **argv
  ){
    assert(argc == 1);
    if(sqlite3_value_type(argv[0]) == SQLITE_NULL) return;

    int n = sqlite3_value_int(argv[0]);
    // doubles the provided argument
    int bonked = n * 2;
    
    sqlite3_result_int(context, bonked);
}

#ifdef _WIN32
__declspec(dllexport)
#endif
int sqlite3_myext_init(
    sqlite3 *db, 
    char **pzErrMsg, 
    const sqlite3_api_routines *pApi
  ){
    int rc = SQLITE_OK;
    SQLITE_EXTENSION_INIT2(pApi);
	
    rc = sqlite3_create_function(
        db,
        "bonk", // name of the function to call in SQLite
        1,
        SQLITE_UTF8|SQLITE_INNOCUOUS|SQLITE_DETERMINISTIC,
        0,
        bonk, // the function to execute
        0,
        0
        );
    return rc;
}
```

https://stackoverflow.com/questions/6663124/how-to-load-extensions-into-sqlite

```sql
select load_extension("myext.so");
-- or if custom symbol (ie. function is not 'sqlite3_extension_init')
select load_extension("myext.so", "sqlite3_myext_init");

-- use the custom function
select bonk(5)
> 10
```

