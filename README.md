# myhttp

作为一个初学者，这是一个比较好练手的项目，自己动手搭建一个服务器，在Linux系统上开发的epoll多路转接的B/S服务器，目前只是实现了一些简单的功能，虽然还是会有些bug,后续会继续有所完善。在书写的过程中碰到的问题我也会在末尾写出，解决方法也会写出
一共分为两个模块：
第一个模块是：epoll监听树，对lfd,cfd进行监听
第二个模块是：浏览器与客户端进行通信，其中包括数据的解析，协议的包装，数据的发送，解码，加码等操作
   

主要函数及其说明：
void epoll_run()；epoll多路转接总流程
int init_listen_fd(int epfd)；初始化服务器端，监听lfd
void do_accept(int lfd,int epfd);与客户端建立连接，监听cfd
void disconnect(int cfd,int epfd);与客户端断开连接
void do_read(int cfd,int epfd);与客户端进行数据交换


void send_respose(int cfd ,int no,char* discripter,char* type,int len)；发送http协议首部
void send_error(int cfd,int status,char*title,char *text)；发送http错误页面
void http_request(int cfd ,const char* filename)；处理http请求

   
void send_file(int cfd,const char* file)；发送普通文件
void send_dir(int cfd,const char* filename)；发送目录
int getmyline(int cfd,char* buf,int size)；获取文件一行的内容
char* get_file_type(const char* name)；获取文件的类型

void encode_str(char* to,int tosize,const char*from)；对发送到网络的数据进行编码
void decode_str(char* to,char* from)；将从网络中收到的东西进行解码
int hexit(char c)；
        
碰到的错误：
   1.在解析http协议后，可以拿到文件路径，调用stat函数查看当前文件的属性时，stat函数报错：找不到对应的文件或者目录，但是我在运行服务器的时候已经指定的服务器的绝对路径，且在函数服务器内部chdir把文件路径改成了指定路径
   目前解决方法：stat函数中传入绝对路径，即/tmp/makefile/homework/HTTP/myhttp.c,但给我的感觉是他把我的根目录作为服务器的目录，我指定的文件路径没有什么用处
   2.在浏览器申请读取mp3文件的过程中，服务端显示读到了数据但会报错send函数对端先行终止，浏览器无法自动播放!
   目前解决方法：因为是send函数出错，因此根据send返回值判断其error!
   3..在访问带有汉字的文件内容时，应该在服务器回发数据给浏览器时进行编码操作，在浏览器请求资源目录的汉字文件时进行解码操作，否则会乱码
   	汉字：Unicode编码 urL：utF－８

