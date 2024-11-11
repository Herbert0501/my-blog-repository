
### 不允许在for循环的初始化声明中使用int类型的变量。

**使用更高的C标准：** 添加编译选项 `-std=c99` 或 `-std=gnu99` 来指定使用C99标准或GNU C99标准。在你的命令中加入这个选项，例如：

```cobol
make CFLAGS=-std=c99 
```

或

```cobol
make CFLAGS=-std=gnu99 
```

### make文件出错，提示“最后的链结失败：输出不可表示的节”

错误原因应当是我当前的 Linux 版本默认启用了 `PIE`，但是 makefile 里的这些库（指上面没有编译成功的那部分库）不支持 PIE，所以导致了上面的错误。

解决方法就是，在编译时禁用 `PIE` 即可，添加参数 `-no-pie`。

### g++: 错误：unrecognized command line option ‘-no-pie’


gcc、g++版本过低，升级版本即可。