翻译自：[Avoiding complexity with systemd | Michael Maclean (mgdm.net)](https://mgdm.net/weblog/systemd/)



Unix 机器，从操作系统的诞生早期，即被设计为支持多用户同时使用。传统上，普通用户和系统服务，被设计为 “非特权用户”；而 root 用户，则被赋予执行有关系统的任何行为的权限。由于 Unix 中的大多数内容，均以文件的概念表示，因此，可以通过将用户添加到文件系统的组，以允许用户执行各种操作。还有其他函数，无法以这种方式委托，特别是绑定到某些 IP 端口。多年来，各种操作系统，都模糊这些内容。特别是在 Linux 上，这具有与 ACL 相同的功能，允许比标准的 Unix 权限模型，提供更多的控制。