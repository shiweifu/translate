翻译自：[https://blog.alexellis.io/building-a-linux-desktop-for-cloud-native-development/](https://blog.alexellis.io/building-a-linux-desktop-for-cloud-native-development/)

本文主要是讲构建基于 Linux 桌面，面向 Cloud Native 开发的计算机。内容包括外设，命令终端，SaaS 软件，一些链接和我所知道的方方面面。希望您能喜欢我的这些经验，学到一些知识，并能构建自己的 Linux 桌面。

我的主要用途是客户端工作和开发开源软件。与客户或者来自社区的小伙伴语音，我主要使用 Zoom，用到的编程语言包括 Java、Go、Python 以及 JavaScript。我也会为 Kubernetes 和云服务做一些构建基础设施的工作，比如测试 Kubernets 控制器和使用 Docker 构建容器镜像之类的。

![image.png](https://upload-images.jianshu.io/upload_images/3115-024336fa1a8d228c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

我需要系统快速、高效、安静，并且最重要的是满足的是满足我的需求。这篇文章的目的不是为了让开发人员相信 Linux 是开发人员所需要的唯一系统，大多数人会更喜欢 Mac 作为通用计算机。

![image.png](https://upload-images.jianshu.io/upload_images/3115-b612b2fc7c9deaf7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

> 如果你安装了一个 Linux 环境，或者有一台运行着 Linux 的电脑，你可以跳过「构建」小节，直接看工具和工作流的相关小节。

### 我知道，它是一个 Linux 系统

在使用命令行终端、编译二进制文件，或者网页浏览， 这些日常使用方面，Linux 桌面和系统的响应比 Mac 的响应更加快速。这可能有些主观，或者是安慰剂效用。但如果安慰剂可以治愈我的 Mac 滞后症，为什么不呢？

况且，我的主要构建的内容，是在云服务上 的Linux 主机上，而不只是在 Mac 上。随着 Cloud、Microservices 以及 Serverless 的星期，为什么不将代码运行在 Linux 主机上呢？

![image.png](https://upload-images.jianshu.io/upload_images/3115-896a9c6d9c9e26d5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

> 我的 Intel NCU 幽冥峡谷，在我编辑这篇文章的时候正在安静的运行。2018款 Mac Mini 作为对比。

Kubernets Go 控制器是巨大的二进制文件，所以 Go Team 不得不向这套工具链中添加一些特殊的扩展。我的经验是在 Mac 平台交叉编译 Linux 二进制文件会话花费很长时间，这会打乱开发人员渴望快速得到反馈的开发节奏。如果您再进一步，使用 Docker 容器进行编译，那将运行一个小型的 Linux VM，可能具有 1 G内存和 2个CPU内核，这将对生产效率产生不利影响。

###  选择 Linux 发行版

与Linux 不同，macOS 的很多设计是为了让普通用户易于上手；而选择 Linux 发行版则需要做一些权衡和取舍。我不太认为所有软件都应该从源代码编译构建，所以 (Gentoo)[https://www.gentoo.org/] 对我来说有点太硬核了。(Arch Linux)[https://www.archlinux.org/] 是个很好的平衡，系统运行快速，可以安装最少的包。但是它是基于滚动更新的。如果更新导致出了一些问题，如果不是简单的问题，有需要好几天来修复。虽然这套机制已经稳定运行了数年，不太可能出问题，让人信服，但我更加推荐 LTS 版的 Ubuntu。它似乎可以完成工作，以及快速的安装、重新安装。有些人也许更喜欢 CentOS 和 Fedora，我更习惯 Ubuntu/Debian 系风格。

Ubuntu 提供一个相对小尺寸 ISO 下载，可以快速的写入 U 盘以及安装。我不太确定它是何时被增加的，但这个「安装一个最小系统」的选项用着挺好。

### 失败的尝试

在决定使用 Intel NUC Hades Canyon 作为主机之前，我犯了一些错误。

首先，我从 eBay 上买了一个「新瓶装旧酒」的 Intel NUC，运行的是 8 代或者 9 代 i5 CPU。我买它的一个很大的原因是有一台 5 代 i5 的 NUC ，被我用于编译服务器。

这台机器的配置是双核CPU，16GB 内存，以及 120G m2 SSD。然而我遇到一些问题：插入 U 盘之后，Ubuntu 不能从 USB 引导，以及集成显卡在滚动的时候，出现阻塞和延迟。因为不能退货，我决定把它放在我的内网，作为我的 Kubernets 集群，运行一些 24/7 的测试。

![image.png](https://upload-images.jianshu.io/upload_images/3115-8ed1e0561df28ae1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

[Intel BXNUC10i7FNK Core i7-10710U 6-Core NUC Mini PC (Slim Version)](https://www.amazon.com/gp/product/B083G6S7HZ/ref=as_li_tl?ie=UTF8&camp=1789&creative=9325&creativeASIN=B083G6S7HZ&linkCode=as2&tag=alexellisuk-20&linkId=f507dcc9d450b5ff97674ac72f998829)

使用10代处理器的这代NCU，拥有六核处理器，而我之前那台NCU是双核的。这提供更好的体验，而我也刷了最新的固件。

然而我遭遇了第二次失败……心灰意冷的我买了一台联想的mini PC。我被 AMD 的Ryzen 系列吸引，这个 CPU 系列具有很高的性价比。

![
](https://upload-images.jianshu.io/upload_images/3115-280d425ade4f36ca.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

我有点受不了这台机器的风扇策略，风扇整天整天的转。这台设备具有很高的主频，然而不管我是打开终端窗口，还是用 Chrome 打开新 Tab，还是做一些其他没有造成压力的事，风扇都一直在转。幸运的是联想已经注意到了这个问题，我研究了一下亚马逊的购买评论，Intel 版本的机器，散热这方面表现好很多。

### The parts-list

继续看硬件。

考虑到我使用最便宜 NUC 时，集成显卡的糟糕体验，我将目光转向了 Hades Canyon（冥王峡谷） 系列。这个系列有两个版本，一个面向游戏（NUC8i7HNK），一个面向VR（NUC8i7HVK），使用的是 AMD 显卡。

我选择了更便宜的 NUC8i7HNK，差价有 150-200 USD，灵一个选择的理由是它所需要的电源功率更小。

可以在亚马逊上直接购买，也可以货比三家。

![image.png](https://upload-images.jianshu.io/upload_images/3115-34f23c51fc3c2acc.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

[Intel NUC 8 VR NUC8I7HVK Gaming Mini PC - 8th Gen Intel Quad-Core i7-8809G Processor up to 4.20 GHz, 32GB DDR4 Memory, 512GB NVMe Solid State Drive, AMD Radeon RX Vega M GH Graphics, Windows 10 Pro](https://www.amazon.com/gp/product/B07P89VZMF/ref=as_li_tl?ie=UTF8&camp=1789&creative=9325&creativeASIN=B07P89VZMF&linkCode=as2&tag=alexellisuk-20&linkId=00ddfdbaf9ac148117e4b7442e2b3623)

- RAM - 我选择的是 32GB 内存

![image.png](https://upload-images.jianshu.io/upload_images/3115-1a6b5c4495f9a8d9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

组建双通道内存 [CORSAIR Vengeance Performance 32GB (2x16GB) 260-Pin DDR4 SO-DIMM DDR4 2666 (PC4 21300) Laptop Memory Model CMSX32GX4M2A2666C18](https://www.amazon.com/gp/product/B01BGZEVHU/ref=as_li_tl?ie=UTF8&camp=1789&creative=9325&creativeASIN=B01BGZEVHU&linkCode=as2&tag=alexellisuk-20&linkId=d44578d1bc552370ed7f49c89bee3a36) 更值得推荐

- Storage

![image.png](https://upload-images.jianshu.io/upload_images/3115-e2ad3d5eddecc930.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

我使用两块三星 Evo Plus m2 SSDs，一块安装 Windows 10 Pro，一块安装 Ubuntu 18.04。

如果你只需要使用 Linux，那 1TB 更给力。你也可以选择两块同样的硬盘，通过 [mdadm](https://linux.die.net/man/8/mdadm) 来组建 RAID-0 磁盘阵列。

使用 Intel 冥王峡谷构建系统，已经基本完成。我们不必操心选择主板，选择高性能的PSU（我感觉是想说CPU），或者什么样的显卡。我们只需要选择所需性能的 NUC 模具，然后添加内存和硬盘即可。

英特尔 NUC 随机附送了一个内六角扳手，用于拧开顶板上的 6 个独立螺丝，把这几个螺丝拧下来之后，然后拔出一个小的扁平连接器，再拧下一颗螺丝。然后，即可简单的安装内存和硬盘。

> 我也同样给我的 Mac Mini 2018 升级了 32GB 内存，那台机器的升级体验非常糟糕，为此，我甚至还买了 5 美元的专用工具。

我购买这套系统纯粹是为了连接客户端以及进行 OSS 的工作。如果您是自雇人士或者有业务需求，你可以咨询一下您的会计，这样的系统是否能抵扣所得税。

###  安装以及第一次启动

我还有一台上一代的 Intel Hades Canyon，相比之下，那台机器体积更小，更纤薄。很高兴看到这一代的重新设计了散热。我要做的第一件事，是进入 BIOS，找到「安静」的 CPU 散热设置。你也许会听到比预期更大的风扇声音，宽敞的后通风口在不断吹出热风。而不是便宜的 CPU 散热器发出的嘶哑声。

![image.png](https://upload-images.jianshu.io/upload_images/3115-ba16189f8d6da086.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

[SanDisk 32GB Ultra Fit USB 3.1 Low-Profile Flash Drive (2 Pack Bundle) SDCZ430-032G-G46 Pen Drive with (1) Everything But Stromboli (TM) Lanyard](https://www.amazon.com/gp/product/B07C4BCH1B/ref=as_li_tl?ie=UTF8&camp=1789&creative=9325&creativeASIN=B07C4BCH1B&linkCode=as2&tag=alexellisuk-20&linkId=356221f0820b71b687fcdf8bd862b178)

我下载了[Ubuntu Desktop 18.04.3 LTS](https://ubuntu.com/download/desktop)，写入到了 U 盘中。

之前尝试使用早一点版本的内核安装 Ubuntu，由于 AMD 显卡驱动的问题，使我无法进入安装界面。解决方法冗长而令人沮丧，这意味着我推迟了构建工作。好消息是，现在内置的 5.x 内核完全支持 AMD 显卡。

我进入安装程序的小技巧是在 Grub 菜单上按 ESC，在内核参数最后加上了 `nomodeset`。[我的理解是 nomodeset 阻止了硬件图形驱动的加载](https://askubuntu.com/questions/1024895/why-do-i-need-to-replace-quiet-splash-with-nomodeset/1024897)，通过 CPU 来进行图形渲染。这会给 CPU 增加一些负担，但是可以暂时绕过这个问题。

我推荐使用 minimal 方式安装，这种方式节省空间，只安装最少的东西。一些有用的系统命令缺失，如 `git` 等。但是我们可以通过一条命令很容易的安装回来。比起装了一大堆用不到的包，然后再删除，这种方式容易的多。

### 安装系统包

运行 `sudo apt update`，然后是 `sudo apt install`

- `tmux` - 一个终端复用工具，断开连接后，命令可以继续运行

看我的视频 [you need to know tmux](https://www.youtube.com/watch?v=JeOSpnT29go) 来了解 tmux

- curl - 是的，这个可能也没装，安装它，用来发送网络请求
- git - 构建开源软件的基石

如果你的 Github 账号开启了两步验证，你不能使用 HTTPS 来访问网络请求，而只能使用 SSH 的方式。

如果你没有 SSH key，使用下面的命令生成一个：

```
ssh-keygen
```

然后登录你的 [GitHub](https://github.com/) 账号，在设置里贴入 `~/.ssh/id_rsa.pub` 中的公钥。

添加完毕后，你就可以使用 GitHub 界面上的 SSH 了。

![image.png](https://upload-images.jianshu.io/upload_images/3115-abd00adb19574ee5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

运行 `git remote rm origin` 来删除已经存在的「remote」地址，然后使用「git remote add origin git@github.com:alexellis/k3sup.git」来添加。

- `htop`  - 美观的展示系统工作情况
- `vim` - 我推荐使用这个编辑器，如果你学习了一些快捷键，你可以使你的工作流更加高效
- `gimp` - 写日志的时候，我使用 gimp 进行一些图片处理，比如缩放大小，或者裁剪。下面这张 htop 的图就是使用 gimp 处理的

![image.png](https://upload-images.jianshu.io/upload_images/3115-3474b8cf26493414.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

> 看到我的内存使用情况不要惊讶，Kubernets 很占资源。

```
sudo apt update && \
  sudo apt install -qy tmux curl git htop vim gimp
```

### 获取图形 App

现在需要下载一些图形 App，我们将下载 deb 文件，然后双击安装它们。从 Chrome 开始 吧。

- `chrome` - [https://www.google.com/intl/en_uk/chrome/](https://www.google.com/intl/en_uk/chrome/)

> 比 Firefox 更加快速的浏览器，抱歉啦 火狐（并不

- `Zoom` - [https://zoom.us/download#client_4meeting](https://zoom.us/download#client_4meeting)

> 如果你需要与其他客户开会，或者参加 OSS 活动，Zoom 是个很好的选择。如果你选择商业版本，还可以抵税。[使用我的注册码](http://bit.ly/2Uimpw9)

- `VSCode` - [https://code.visualstudio.com/download](https://code.visualstudio.com/download)

安装 VSCode 之后，可以添加一下拼写插件。日常输入错别字再说难免，最好今早发现他们。

- `Slack` - [https://slack.com/intl/en-gb/downloads/mac?geocode=en-gb](https://slack.com/intl/en-gb/downloads/mac?geocode=en-gb)

[OpenFaaS Slack](https://slack.openfaas.io/)，这是一个 Slack 中的工作空间，你可以在这里讨论 Kubernets，Docker，ARM，Raspberry Pi，inlets，k3s，以及其他的内容。通过下载及安装 Slack，你可以登录账号以及加入这些空间。

![image.png](https://upload-images.jianshu.io/upload_images/3115-8f2680ac09eee70f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

4K 提供了巨大的桌面空间，终端在高分辨率下也很漂亮。

### 获取其他 Linux Apps

有的 App ，是通过下载 deb 文件进行安装，有的不是。

- `docker` - 编译、运行以及发布镜像

```
curl -sSLf https://get.docker.com | sudo sh

sudo usermod -aG docker $(whoami)
```

- `docker-compose` - compose 是开发者在本地开发时的最爱。可以通过 pip 安装，依赖Python。

```
sudo apt install -qy python3 python3-pip

sudo pip3 install docker-compose
```

- `inletsctl` - [inlets](https://inlets.dev/) 是你的 Cloud Native 通道，可以在需要时获取公共 IP。可以与您的团队、你的客户以及社区共享工作。它类似 Ngrok，但是更现代，以及免费。

```
curl -sSLf https://inletsctl.inlets.dev | sudo sh
inletsctl download
inletsctl download --pro
```

![image.png](https://upload-images.jianshu.io/upload_images/3115-342ce0ebd2895a5b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

inlets-pro 可以通过隧道传输纯 L4 TCP 流量，如果您想免费试用，[请在此处申请](https://github.com/inlets/inlets-pro#getting-a-license-key)。引用本文，我将免费为您的许可证试用期加倍时长。

- `k3d` - 在  k3s 上构建可在 docker 容器中运行 整个 Kubernetes 集群的最快选项，占用内存最少。

```
curl -s https://raw.githubusercontent.com/rancher/k3d/master/install.sh | bash
```

- `k3sup` - k3sup 可以在远程 VMs 上安装 k3s，比如你的树莓派集群。也可以用于安装 Kubernets Dashboard，MiniSo，Postgresql，OpenFaaS，cert-manager 等

```
curl -sSLf https://get.k3sup.dev | sudo sh
```

- `kubectx` - kubectx 是一个 bash 脚本，用于快速切换 Kubernets 环境。你可能需要切换本地或者远程集群。

```
cd /tmp/
git clone https://github.com/ahmetb/kubectx
chmod +x ./kubectx/kubectx
sudo cp ./kubectx/kubectx /usr/local/bin/
```

- `hub` - 如果你是一名系统管理员，或者曾在 OSS 项目中测试 PR，你会需要 GitHub 的 Hub 命令行程序。

```
curl -sSL https://github.com/github/hub/releases/download/v2.14.1/hub-linux-amd64-2.14.1.tgz > /tmp/hub.tgz
sudo tar -xvf /tmp/hub.tgz -C /usr/local/ --strip-components=1
```

我最喜欢的命令是 `hub pr list/checkout`，以及 `hub issue`

![image.png](https://upload-images.jianshu.io/upload_images/3115-75d8e0114ae3d977.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- Golang - 安装 Go 是我的工作流中必不可少的关键部分，如果你想为 Cloud Native 工具作出贡献，则应提早安装以准备就绪。

```
sudo mkdir -p /usr/local/go/
curl -sSL https://dl.google.com/go/go1.13.7.linux-amd64.tar.gz > go1.13.7.linux-amd64.tar.gz
sudo tar -xvf go1.13.7.linux-amd64.tar.gz -C /usr/local/go --strip-components=1
```

编辑 `~/.bashrc`：

```
export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin
export GOPATH=$HOME/go
```

然后建立 GOPATH 文件夹，`mkdir -p $HOME/go`。

确保可以工作：`go version`

现在可以给 VSCode 安装 "Go" 插件，首先会下载 Go 静态分析工具以，然后安装到你本地的 $PATH 目录中。

- Node.js - 即使你不是一名 JavaScript 开发者，安装一个 node 在系统中也很有用。特别你是需要一个终端计算器的时候。

```
curl -sSL https://nodejs.org/dist/v12.14.1/node-v12.14.1-linux-x64.tar.gz > node-v12.14.1-linux-x64.tar.gz
sudo tar -xvf node-v12.14.1-linux-x64.tar.gz -C /usr/local --strip-components=1
```

`node` 作为计算器的例子：

```
alex@nuc7:~$ node
Welcome to Node.js v12.14.1.
Type ".help" for more information.
> day_rate=2500
2500
> day_rate*1.1
2750
>
```

### 其他设备

对于整个工作环境，你还需要一些其他硬件设备。这部分的参考性一般，因为你可能已经有了所需要的其他硬件，如显示器，键盘以及鼠标。

![image.png](https://upload-images.jianshu.io/upload_images/3115-af5542d078a00b90.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

我之前使用的是一台 Dell UltraSharp 27'' 显示器，我很喜欢它，这次我想找一个便宜一些的替代品。然后我发现了明基显示器。这个系列提供了和 Dell 几乎一致的使用感受，只是外壳不一样。

![image.png](https://upload-images.jianshu.io/upload_images/3115-0ebcd793686f6ac4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

[BenQ PD2700U 27 inch 4K UHD IPS Monitor | HDR | 100% sRGB and Rec. 709 | AQColor Tech for Accurate Reproduction](https://www.amazon.com/gp/product/B07H9XP92N/ref=as_li_tl?ie=UTF8&camp=1789&creative=9325&creativeASIN=B07H9XP92N&linkCode=as2&tag=alexellisuk-20&linkId=69c1f4594f071dabf0937168ebf79d47)

我尝试了一些操作，Ubuntu 的桌面缩放都工作的很好。我使用一根标准 HDMI 数据线进行连接。IPS 面板看起来很漂亮，可视角度也很好。

![image.png](https://upload-images.jianshu.io/upload_images/3115-a0d9a4ed0ae478bd.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

[USB C to DisplayPort Cable 4K@60Hz](https://www.amazon.com/gp/product/B075V27G2R/ref=as_li_tl?ie=UTF8&camp=1789&creative=9325&creativeASIN=B075V27G2R&linkCode=as2&tag=alexellisuk-20&linkId=302d9eff922db82a804dc5c1e4fbed8b)

显示器有三个信号输入接口：HDMI，DisplayPort 以及一个 Mini DisplayPort。我通过一根 HDMI 连接我的 NCU，通过 USB-C-DisplayPort 连接我的 Mac Mini，都使用 4K 60HZ。

接下来进入输入设备部分 - 机械键盘以及专业的鼠标。

![image.png](https://upload-images.jianshu.io/upload_images/3115-b7721c566b40e1ae.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

[Logitech MX Master 3 Advanced Wireless Mouse - Graphite](https://www.amazon.com/gp/product/B07S395RWD/ref=as_li_tl?ie=UTF8&camp=1789&creative=9325&creativeASIN=B07S395RWD&linkCode=as2&tag=alexellisuk-20&linkId=3bc61544e2e7690efc1e7a8be459a537)

幸运的是 NUC 集成了一个蓝牙芯片，使用蓝牙不需要额外的蓝牙接收设备。我手头有一只通过蓝牙连接的 MX Master 2 鼠标。现在如果买的话，可以买到这只鼠标 2019/2020 的升级版，MX Master 3。

机械键盘是我最重要的物品之一，我尝试过许多不同的机械轴，最对我胃口的是 MX 银轴--它安静，手感好，以及反馈及时。

[Durgod Taurus K320 TKL Mechanical Gaming Keyboard - 87 Keys - Double Shot PBT - NKRO - USB Type C](https://www.amazon.com/gp/product/B078H3WPHM/ref=as_li_tl?ie=UTF8&camp=1789&creative=9325&creativeASIN=B078H3WPHM&linkCode=as2&tag=alexellisuk-20&linkId=461e3c710ce8a7eb4a877f8381561936)

如果只能改善工作流中的一个环节的话，升级你的键盘是提升最明显的。

[Logitech C922x Pro Stream Webcam – Full 1080p HD Camera](https://www.amazon.com/gp/product/B01LXCDPPK/ref=as_li_tl?ie=UTF8&camp=1789&creative=9325&creativeASIN=B01LXCDPPK&linkCode=as2&tag=alexellisuk-20&linkId=eb9b4bcd02af08b68a5d0b6a36d3d9a6)

最后一个我认为必要的东西是一个给力的 USB hub，一个带独立供电的 hub 可以带动多个周边设备，以及提供手机的快速充电。

![image.png](https://upload-images.jianshu.io/upload_images/3115-efb7f5e25429b244.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

[Anker 10 Port 60W Data Hub with 7 USB 3.0 Ports and 3 PowerIQ Charging Ports](https://www.amazon.com/gp/product/B00VDVCQ84/ref=as_li_tl?ie=UTF8&camp=1789&creative=9325&creativeASIN=B00VDVCQ84&linkCode=as2&tag=alexellisuk-20&linkId=8a586bff2afe48f1791ed00aa2a7dd15)

使用 USB hub 使我可以在 Mac 与 Linux 之间，快速的切换我的设备，如键盘、鼠标、摄像头、麦克风以及其他连接在上面的设备。

## 远程工作与语音通话

我使用一台罗技的摄像头，与我的客户进行通话。你可以找到一些更新型号的选择，不必参考我的选择。

许多开发者推荐 Blue Yeti 麦克风。我不太喜欢使用，因为它对噪音非常敏感。

![image.png](https://upload-images.jianshu.io/upload_images/3115-f6ae8113ababa1f0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

我推荐 [Audio-Technica AT2020+ Cardioid Condenser USB Microphone, Black](https://amzn.to/38iaPFN)，它配有三脚架和方便携带的手提箱。这意味着旅行或在出差去外地开会的时候，我也可以带上它，给我录制的视频，再录制一些高质量的音频内容。它使用 USB 进行连接，方便在多台电脑上切换。

![image.png](https://upload-images.jianshu.io/upload_images/3115-8fd2bfd8a65c033a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

远程工作中，离开办公室，去感受一下新鲜的空气是非常重要的。[新版本的 Apple AirPods Pro](https://www.amazon.com/gp/product/B07ZPC9QD4/ref=as_li_tl?ie=UTF8&camp=1789&creative=9325&creativeASIN=B07ZPC9QD4&linkCode=as2&tag=alexellisuk-20&linkId=ba28103125eafa1683616e0c2019c972) 有着非常好的降噪效果，通过蓝牙连接我的手机、电脑或者 NCU。我将在后续的日志中介绍「旅途中的工作」。

最后一节值得一说的，是办公软件，或者叫办公套件。

![image.png](https://upload-images.jianshu.io/upload_images/3115-10626214efcdb6eb.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

我使用 Google 全家桶作为办公套件，我绑定了公司的域名： openfaas.com。我用到了以下部分：

- Google Docs - 为客户或者会议编写提案或者说明书。协作功能非常直观，对异步工作很有帮助。我还通过共享文档跟踪 OSS 的一些记录。

- Google Sheets - 用于追踪消失/天以及项目进度。如果你是个新的咨询师，请根据产出价值/时间定价。

- Google Mail - 绑定了 openfaas.com后，允许使用多个收件箱分割不同账户，如 sales@ 和 support@。你可以为你的商业活动开一个独立的邮件地址。

- Google Calender - 安排会议和通话。我也用它安排产品研发和休息时间。

[查看 Google 全家桶](https://refergsuite.app.goo.gl/C8vM) 价格，免费使用我的邀请码，基础基础版：`UAE7QWH7XPQRG3C `，商业版： `V9VKLERQ3E3TDQG `。

使用 Google 全家桶的额外好处是，在任何设备，任何时间都可以使用，具有很高的可用性。

### 总结

这篇文章中，我总结分享了一些关于「在 2020 年，如何配置和运行一台 Linux 的桌面系统的电脑」的一些经验和选择的理由；我尝试把硬件构建，以及围绕着「Cloud Native」展开介绍工作流的工具选择和经验都说清楚。希望你可以喜欢。

那么接下来你想看了解点什么？好奇哪些东西？或者是我拉下了什么？

如果你有问题、评论交流或者建议，可以通过我的 Twitter [@alexellisuk](https://twitter.com/alexellisuk/) 联系到我。

你可以在 GitHub 上，找到我参与的 OSS 项目，如果你有兴趣使用，或者想参与其中，或者只是想和志趣相投的社区建立联系，那就加入 [OpenFaaS Slack](https://slack.openfaas.io/) 吧。

> 如果你喜欢这篇日志，你可以通 [GitHub Sponsors](https://github.com/sponsors/alexellis) 来订阅，你将可以获取：关于我 OSS 项目的电子邮件简报（包含文章更新），我的工作，以及一些教程。

















