# SERV00部署Web页的RSS阅读器

## SERV00网站部分
### Enable Run your own applications
### 开放端口
Port : random
Port : tcp
### 新建反代类型站点
Add Website
Domain: rss.xxxx.xxxx.xxxx
Website type : Proxy
Proxy target : localhost
Proxy Port : < previous random generate port number>
use HTTPS: unchecked
DNS support : checked
### 添加A记录 (cloudflare side)
rss.xxxx.xxxx.xxxx : < ip from DNS zone A record>
代理状态:OFF
### 申请证书 (Serv00 side)
Add Certificate
Type: "Genertate Lets Encrypt Certificate"
Domain: rss.xxxx.xxxx.xxxx

## SSH终端部分
Connect Serv00 with SSH tools.
### 克隆仓库
```
cd ~/domains
git clone https://github.com/nkanaev/yarr
cd yarr
```
### 编译FreeBsd可执行二进制文件
```
make build_default
cp _output/yarr ./
```
It takes time to build the execute.

### 制作执行脚本
```
echo '#! /bin/bash
nohup ~/domains/yarr/yarr -addr 0.0.0.0:<port> -auth <user name>:<password> >/dev/null 2>&1 &' > rss.sh && chmod +x rss.sh
```

### 保活步骤
```
crontab -e

#添加如下内容
@reboot bash /home/renyuanceshi/domains/yarr/rss.sh
```
## 贡献

|姓名|主页|内容|
| :------------: | :------------: | :------------: |
|米拉一 (Milaone Channel)|https://youtu.be/Kjd8mNwy6SE|SERV00部署Web页的RSS阅读器|

<br />
<br />
