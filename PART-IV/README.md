# NASM

书中的安装过程就不介绍了，只简单记录一下一些使用的细节。

## 安装

### 在 OSX 中安装

直接通过 [brew](https://brew.sh/) 即可安装：

```
brew install nasm
```

### 在 Ubuntu 中安装

直接通过 apt-get 安装:

```
apt-get install nasm
```

## 在 docker 中使用 NASM

虽然在 OSX 中也可以使用 nasm，不过书中的代码 io.mac 似乎只能在 linux 下工作。所以在 OSX 下面，我们可以通过 docker 安装 ubuntu 镜像来使用 nasm。

拉取 ubuntu 镜像:

```
docker pull ubuntu
```

启动镜像使用下面的命令:

```
docker run -it --network host -v ~/Downloads/asm_programs:/var/asm --cap-add=SYS_PTRACE --security-opt seccomp=unconfined nasm
```

命令的几个部分意义分别是:

`--network host` 使用宿主机的网络接口。

`-v ~/Downloads/asm_programs:/var/asm` 将宿主机的 `~/Downloads/asm_programs` 目录映射到容器的 `/var/asm` 目录，这样在容器中可以直接访问宿主机目录下的内容。

`--cap-add=SYS_PTRACE --security-opt seccomp=unconfined` 容器的设定，容器默认会使用 linux 的内核保护模式，在这个模式下 gdb 将不能调试我们的汇编程序，这样设定之后，将关闭容器中的 linux 内核保护模式，从而可以使用 gdb 来进行程序调试。