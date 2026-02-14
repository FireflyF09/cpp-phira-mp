# phira-mp-server (C++ 版，使用Claude编写）

C++版的 [phira-mp](https://github.com/TeamFlos/phira-mp) 

## 特点

 大概与原版相同


## 环境

 需要以下包（Ubuntu、debian等）

- **g++** (>= 13, C++20 支持)
- **curl** 
- **make**

On Ubuntu 24.04/debian:
```bash
sudo apt-get install -y g++ make curl
```

## 编译

```bash
make
```

接着会生成`phira-mp-server` 二进制文件。

## 运行

```bash
# Default port (12346)
./phira-mp-server

# Custom port
./phira-mp-server --port 8080

# With verbose logging
RUST_LOG=debug ./phira-mp-server --port 12346
```

日志等级有: `trace`, `debug`, `info` (默认), `warn`, `error`

## 配置

创建 `server_config.yml` 在工作目录中:

```yaml
monitors:
- 2
- 42
```

以上这个不知道是干嘛用的


## 协议

MIT协议

