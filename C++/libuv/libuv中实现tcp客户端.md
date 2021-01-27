```c
#include <stdio.h>
#include <uv.h>
#include <stdlib.h>

uv_loop_t *loop;
#define DEFAULT_PORT 8080

uv_tcp_t mysocket;

//负责为新来的消息申请空间
void alloc_buffer(uv_handle_t *handle, size_t suggested_size, uv_buf_t *buf)
{
    buf->len = suggested_size;
    buf->base = static_cast<char *>(malloc(suggested_size));
}

void echo_write(uv_write_t *req, int status) {
  if (status) {
    fprintf(stderr, "Write error %s\n", uv_strerror(status));
  }

  free(req);
}

void on_close(uv_handle_t *handle) {
  if (handle != NULL)
    free(handle);
}

void read_cb(uv_stream_t *client, ssize_t nread, const uv_buf_t *buf) {
  if (nread > 0) {
//    buf->base[nread] = 0;
    fprintf(stdout, "recv:%s\n", buf->base);
    fflush(stdout);

    uv_write_t* req = (uv_write_t*)malloc(sizeof(uv_write_t));

    uv_buf_t uvBuf = uv_buf_init(buf->base, nread);//初始化write的uv_buf_t

    //发送buffer数组，第四个参数表示数组大小
    uv_write(req, client, &uvBuf, 1, echo_write);

    return;
  } else if (nread < 0) {
    if (nread != UV_EOF) {
      fprintf(stderr, "Read error %s\n", uv_err_name(nread));
    } else {
      fprintf(stderr, "client disconnect\n");
    }
    uv_close((uv_handle_t *) client, on_close);
  }

  //释放之前申请的资源
  if (buf->base != NULL) {
    free(buf->base);
  }
}

void on_connect(uv_connect_t *req, int status)
{
    if (status < 0)
    {
        fprintf(stderr, "Connection error %s\n", uv_strerror(status));

        return;
    }

    fprintf(stdout, "Connect ok\n");

    uv_stream_t *socket = req->handle;

    uv_read_start((uv_stream_t *)socket, alloc_buffer, read_cb);
}

int main(int argc, char **argv)
{
    loop = uv_default_loop();

    uv_tcp_init(loop, &mysocket);

    struct sockaddr_in addr;

    uv_connect_t *connect = (uv_connect_t *)malloc(sizeof(uv_connect_t));

    uv_ip4_addr("127.0.0.1", DEFAULT_PORT, &addr);

    int r = uv_tcp_connect(connect, &mysocket, (const struct sockaddr *)&addr, on_connect);

    if (r)
    {
        fprintf(stderr, "connect error %s\n", uv_strerror(r));
        return 1;
    }

    return uv_run(loop, UV_RUN_DEFAULT);
}
```

