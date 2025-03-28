# 使用雨云VPS快速搭建MCSM面板和Minecraft服务器全攻略

## 前言
本教程将详细演示如何在雨云NAT云服务器上通过Docker容器部署MCSM面板和Minecraft服务器（以mohist1.16.5版为例）。采用Docker方案可灵活切换Java版本，方便管理多个MC服务端实例。

## MCSM面板核心优势
- **分布式架构**：支持多物理服务器集中管理
- **开箱即用**：全中文界面，5分钟快速部署
- **高扩展性**：支持Minecraft及其他游戏服务端
- **容器化支持**：完美兼容Docker环境

## 准备工作
### 服务器配置建议
- **基础配置**：1核2G（适合1.16.5及以下版本）
- **推荐配置**：
  - 4G内存起步（1.17+版本）
  - AMD 5900X/13900K等高主频CPU（优化单核性能）
- **操作系统**：Debian/Ubuntu系统

👉 [【点击查看】2025年最新雨云优惠码及特价云服务器方案汇总](https://bit.ly/RainYun)

## 详细部署流程
### 第一步：连接服务器
1. 使用SSH工具（Putty/MobaXterm）连接服务器
2. 输入IP/域名和SSH端口（默认22）
3. 登录凭证：
   - 用户名：root
   - 密码：雨云控制台获取

### 第二步：端口映射配置
| 服务类型 | 内网端口 | 必映射 |
|---------|---------|-------|
| MCSM面板 | 23333 | ✓ |
| 守护进程 | 24444 | ✓ |
| MC服务器 | 25565 | ✓ |

操作步骤：
1. 登录雨云控制台
2. 进入"NAT端口映射"页面
3. 为上述端口创建映射规则

### 第三步：防火墙设置
bash
systemctl stop firewalld
systemctl disable firewalld

## MCSM面板安装指南
### 一键安装方案
bash
wget -qO- https://gitee.com/mcsmanager/script/raw/master/setup.sh | bash
systemctl start mcsm-{daemon,web}
systemctl enable mcsm-{daemon,web}

### 访问面板
- 访问地址：`http://服务器IP:映射端口`
- 首次登录需设置管理员账户

## Docker环境配置
### 安装Docker
bash
apt update && apt install docker.io -y

### 配置镜像加速
bash
mkdir -p /etc/docker
tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": ["https://registry.docker-cn.com"]
}
EOF
systemctl restart docker

## Minecraft服务端部署
### 环境准备
1. 在MCSM面板创建OpenJDK镜像
   - 1.12.2以下：JDK8
   - 1.16.5：JDK11（需手动修改）
   - 1.17+：JDK17

### 服务端配置
ini
启动命令示例：
-Xmx4G -jar mohist-1.16.5.jar -Dfile.encoding=UTF-8
关键参数说明：
- -Xmx4G：限制最大内存
- -Dfile.encoding=UTF-8：防止中文乱码

### 服务器优化
1. 修改server.properties：
   - 关闭正版验证（online-mode=false）
   - 调整最大玩家数
2. 通过端口映射暴露25565端口

## 客户端连接指南
连接格式：`服务器IP:映射端口`
- Java版：直接添加服务器
- 基岩版：需安装GeyserMC插件

## 常见问题解决
1. **启动失败**：检查eula.txt是否改为true
2. **内存不足**：调整-Xmx参数
3. **连接超时**：确认端口映射正确

## 进阶配置建议
- 定期备份世界存档
- 使用Watchdog插件防崩溃
- 搭配BungeeCord实现多服互通

通过本教程，您已成功在雨云服务器上搭建了专业的Minecraft游戏环境。如需更高性能方案，可随时升级服务器配置。