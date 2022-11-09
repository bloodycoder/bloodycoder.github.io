+++
title = "源码剖析 listen"
date = "2022-07-19"
description = "从内核源码分析服务器创建socket到accept的过程"
tags = ["socket","linux"]
+++

{{< toc >}}

# 前言
内核版本linux-5.0.1. released  on 2019年3月4日

主要源码位于socket.c中

```c
//创建socket
int s = socket(AF_INET, SOCK_STREAM, 0);   
//绑定
bind(s, ...)
//监听
listen(s, ...)
//接受客户端连接
int c = accept(s, ...)
//接收客户端数据
recv(c, ...);
//将数据打印出来
printf(...)
```

首先根据`syscall_64.tbl`可以知道bind、listen、accept都是系统调用

```
41	common	socket			__x64_sys_socket
43	common	accept			__x64_sys_accept
...
49	common	bind			__x64_sys_bind
50	common	listen			__x64_sys_listen
```

# socket干了啥

创建了一个文件，初始化socket结构体，并且实现了文件和socket的互相绑定。最终返回文件的文件描述符.

```c
struct socket {
	socket_state		state;
	short			type;

	unsigned long		flags;

	struct socket_wq	*wq;

	struct file		*file; //socket需要和一个文件绑定.
	struct sock		*sk;  //sockets的网络层表示
	const struct proto_ops	*ops;
};
```

socket_state的枚举含义

    ● SS_FREE = 0,			/* not allocated		*/
    ● SS_UNCONNECTED,		/* unconnected to any socket	*/
    ● SS_CONNECTING,		/* in process of connecting	*/
    ● SS_CONNECTED,			/* connected to socket		*/
    ● SS_DISCONNECTING		/* in process of disconnecting	*/

注意，调用socket的返回值的fd由sock_map_fd返回.

![](/images/accept0/1.png)

# bind 干了啥

eee

# listen 干了啥

在listen中的主要任务: 

1. 设置socket状态为TCP_LISTEN
2. 将socket链入半连接队列的Hash表中

![linux_listen_ac.drawio](C:\Users\Picard\Dropbox\draw.io图片\linux_listen_ac.drawio.png)

```
struct sock *tcp_check_req(struct sock *sk, struct sk_buff *skb,
			   struct request_sock *req,
			   bool fastopen, bool *req_stolen)
```

这个看着像三次握手的代码.

# accept 干了啥