通过执行如下命令创建一个最简单的 HTTP 服务器：

```
python -m http.server
```

服务器默认监听端口是 8000，支持自定义端口号：

```
python -m http.server 9000
```

服务器默认绑定到所有接口，可以通过 -b/--bind 指定地址，如本地主机：

```
python -m http.server --bind 127.0.0.1
```

服务器默认工作目录为当前目录，可通过 -d/--directory 参数指定工作目录：

```
python -m http.server --directory /tmp/
```

此外，可以通过传递参数 --cgi 启用 CGI 请求处理程序：

```
python -m http.server --cgi
```
编写代码创建
http.server 也支持在代码中调用，导入对应的类和函数即可。

停止服务
```bash
ps aux | grep httpserver 
kill <pid>
```

```python
from http.server import SimpleHTTPRequestHandler
from http.server import CGIHTTPRequestHandler
from http.server import ThreadingHTTPServer
from functools import partial
import contextlib
import sys
import os


class DualStackServer(ThreadingHTTPServer):
    def server_bind(self):
        # suppress exception when protocol is IPv4
        with contextlib.suppress(Exception):
            self.socket.setsockopt(socket.IPPROTO_IPV6, socket.IPV6_V6ONLY, 0)
        return super().server_bind()


def run(server_class=DualStackServer,
        handler_class=SimpleHTTPRequestHandler,
        port=8000,
        bind='127.0.0.1',
        cgi=False,
        directory=os.getcwd()):
    """Run an HTTP server on port 8000 (or the port argument).

    Args:
        server_class (_type_, optional): Class of server. Defaults to DualStackServer.
        handler_class (_type_, optional): Class of handler. Defaults to SimpleHTTPRequestHandler.
        port (int, optional): Specify alternate port. Defaults to 8000.
        bind (str, optional): Specify alternate bind address. Defaults to '127.0.0.1'.
        cgi (bool, optional): Run as CGI Server. Defaults to False.
        directory (_type_, optional): Specify alternative directory. Defaults to os.getcwd().
    """

    if cgi:
        handler_class = partial(CGIHTTPRequestHandler, directory=directory)
    else:
        handler_class = partial(SimpleHTTPRequestHandler, directory=directory)

    with server_class((bind, port), handler_class) as httpd:
        print(
            f"Serving HTTP on {bind} port {port} "
            f"(http://{bind}:{port}/) ..."
        )
        try:
            httpd.serve_forever()
        except KeyboardInterrupt:
            print("\nKeyboard interrupt received, exiting.")
            sys.exit(0)


if __name__ == '__main__':
    run(port=8000, bind='127.0.0.1')
```
server_class：服务器类
handler_class：请求处理类
port：端口
bind：IP
cgi：是否启用 CGI 请求处理程序
directory：工作目录
————————————————
原文链接：(https://blog.csdn.net/qq_42951560/article/details/124218348)


python -m http.server 11000 --directory /home/gitdev/ssr
