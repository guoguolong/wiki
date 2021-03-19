```c
/**
 *
 *   Linux/macOS 下实现无回显的 getchar
 *
 */
#include <stdio.h>
#include <termios.h>
#include <unistd.h>
#include <errno.h>
#define ECHOFLAGS (ECHO | ECHOE | ECHOK | ECHONL)

// 函数set_disp_mode用于控制是否开启输入回显功能
// 如果option为0，则关闭回显，为1则打开回显
int set_disp_mode(int fd, int option) {
    int err;
    struct termios term;
    if (tcgetattr(fd,&term)==-1) {
        perror("Cannot get the attribution of the terminal");
        return 1;
    }
    if(option) {
        term.c_lflag |= ECHOFLAGS;
    } else {
        term.c_lflag &= ~ECHOFLAGS;
    }
    err = tcsetattr(fd, TCSAFLUSH, &term);
    if (err==-1 && err == EINTR) {
        perror("Cannot set the attribution of the terminal");
        return 1;
    }
    return 0;
}
//函数getpasswd用于获得用户输入的密码，并将其存储在指定的字符数组中
int getpasswd(char* passwd, int size) {
    int c, n = 0;
  
    printf("Please Input password:");
    do {
        c = getchar();
        if (c != '\n' | c!='\r') {
            passwd[n++] = c;
        }
    } while(c != '\n' && c !='\r' && n < (size - 1));
    passwd[n] = '\0';
    return n;
}

int main() {
    char *p, passwd[20], name[20];
    printf("Please Input name:");
    scanf("%s", name);
    getchar();// 将回车符屏蔽掉

    set_disp_mode(STDIN_FILENO, 0); // 首先关闭输出回显，这样输入密码时就不会显示输入的字符信息
    getpasswd(passwd, sizeof(passwd)); //调用 getpasswd 函数获得用户输入的密码
    set_disp_mode(STDIN_FILENO, 1); // 恢复回显

    p = passwd;
    while (*p!='\n') {
        p++;
    }
    *p = '\0';
    printf("\nYour name is: %s", name);
    printf("\nYour passwd is: %s\n", passwd);

    return 0;
}
```