# Serv00 控制面板自动登录脚本

## 使用方法

1. 在 GitHub 仓库中，进入上(右角)`Settings`

2. 在侧边栏找到`Secrets and variables`，点击展开选择`Actions`，点击`New repository secret`
    
3. 然后创建一个名为`ACCOUNTS_JSON`的`Secret`，将 JSON 格式的账号密码字符串作为它的值，如下格式：  

``` json
[  
  { "username": "qishihuang", "password": "zhanghao", "panelnum": "3" },  
  { "username": "zhaogao", "password": "daqinzhonggong", "panelnum": "1" },  
  { "username": "heiheihei", "password": "shaibopengke", "panelnum": "2" }  
]
```

> 其中`panelnum`参数为面板编号，即为你所收到注册邮件的`panel*.serv00.com`中的`*`数值。

## 贡献

|姓名|主页|内容|
| :------------: | :------------: | :------------: |
|lopins|https://github.com/lopins/serv00-auto-scripts|控制面板自动登录脚本|

<br />
<br />

# SERV00 3个必备工具配置

## 安装PM2
安装脚本
```
bash <(curl -s https://raw.githubusercontent.com/k0baya/alist_repl/main/serv00/install-pm2.sh)
```
<br />

## 安装Cloudflared
```
mkdir -p ~/domains/cloudflared && cd ~/domains/cloudflared
wget https://cloudflared.bowring.uk/binaries/cloudflared-freebsd-latest.7z && 7z x cloudflared-freebsd-latest.7z && rm cloudflared-freebsd-latest.7z && mv -f ./temp/* ./cloudflared && rm -rf temp
or
wget https://github.com/renyuanceshi/Serv00/raw/main/cloudflared/cloudflared-freebsd.7z && 7z x cloudflared-freebsd.7z && rm cloudflared-freebsd.7z && mv -f ./temp/* ./cloudflared && rm -rf temp
```
### pm2测试运行cloudflared进程
在cloudflared程序所在目录运行：
```
pm2 start ./cloudflared -- tunnel --edge-ip-version auto --protocol http2 --heartbeat-interval 10s run --token ARGO_TOKEN
```
ARGO_TOKEN 替换成自己真实有效的token

<br />

## 升级go语言环境
### 创建安装目录
```
mkdir -p ~/local/soft && cd ~/local/soft
```
### 下载编译好的 go1.22 的程序包
```
wget https://dl.google.com/go/go1.22.0.freebsd-amd64.tar.gz
```
### 解压
```
tar -xzvf go1.22.0.freebsd-amd64.tar.gz
```
### 删除压缩文件
```
rm go1.22.0.freebsd-amd64.tar.gz
```
### 修改 .profile 文件
```
echo 'export PATH=~/local/soft/go/bin:$PATH' >> ~/.profile
```
### 使 .profile 的修改生效
```
source ~/.profile
```
### 检查 go 版本
```
go version
```
## 贡献

|姓名|主页|内容|
| :------------: | :------------: | :------------: |
|米拉一 (Milaone Channel)|https://www.youtube.com/watch?v=3JIQUa7vi6Q&t=206s|SERV00注册必备的1个关键设置和3个必备工具配置好环境后准备起飞|

<br />
<br />
