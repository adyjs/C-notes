# C best practice


### flush stdio

use function to vent '\n' and 'EOF' in buffer

```c

void clear_stdio(void){
    char c;
    do{
        c = getchar();
    } while(c != '\n' && c != 'EOF');
}

```

