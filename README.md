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

# 部署探针程序哪吒面板
## 面板端（服务端）中添加域名，映射端口
### 新建两个端口
1158:作为web面板端口
1159:作为grpc端口用于与被管理的服务器中agent通讯端口

### 新建2个website
nezha.rycs.us.kg <br />
agent.rycs.us.kg <br />
Website type选择代理，<br />
分别指向localhost的1158端口和1159端口<br />
nezha.rycs.us.kg作为面板的https访问地址<br />
agent.rycs.us.kg作为跟被管理服务器通信使用<br />
然后把这两个个域名的A记录指向serv00的ip，<br />
切记agent.rycs.us.kg切记！<br />
不要开启cdn，不要开启cdn，不要开启cdn<br />

## github中申请OAuth2
登录自己的github账户，来到页面 https://github.com/settings/developers <br />
选择OAuth Apps -->New OAuth App <br />
```
Application name:随便起
Homepage URL:使用面板https地址https://nezha.rycs.us.kg
Authorization callback URL:https://nezha.rycs.us.kg/oauth2/callback
然后Register application按钮，返回应用的相关信息。
记录下获得的id和secret，待会配置文件要用
```

## SSH终端中下载服务器端的二进制文件dashboard
### 建立一个~/nezhacmd目录，作为面板的程序目录
```
mkdir ~/nezhacmd
cd ~/nezhacmd
```

### 下载freebsd编译好的二进制文件最新版，并赋给运行权限
```
API_URL="https://api.github.com/repos/wansyu/nezha-freebsd/releases"
RELEASE_DATA=$(curl -s $API_URL | jq -r '.[0]')
echo "$RELEASE_DATA"
DOWNLOAD_URL=$(echo "$RELEASE_DATA" | jq -r ".assets[] | select(.name == \"dashboard\") | .browser_download_url")
if [ -z "$DOWNLOAD_URL" ]; then
  echo "No asset named 'dashboard' found"
else
  curl -L $DOWNLOAD_URL -o dashboard
fi
chmod +x dashboard
```
如果上面代码运行出错<br />
请到https://github.com/wansyu/nezha-freebsd/releases获取最新版二进制文件地址<br />
使用命令<br />
curl -L 文件地址 -o dashboard<br />
进行下载最新版到~/nezhacmd目录中<br />
然后<br />
chmod +x dashboard<br />

### 准备运行面板所需的文件
#克隆哪吒面板项目目录
```
git clone https://github.com/naiba/nezha.git ~/nezha
cp -r ~/nezha/resource ~/nezhacmd
```

#拷贝配置文件
```
mkdir ~/nezhacmd/data
cp ~/nezha/script/config.yaml ~/nezhacmd/data/config.yaml
```

#删除克隆目录（这个目录没用）
```
rm -rf ~/nezha
```

#编辑配置文件 nano ~/nezhacmd/data/config.yaml
```
debug: false
httpport: 80     #需要修改为1158
language: zh-CN  #需要修改为zh-CN
grpcport: 1159   #需要修改为1159
oauth2:
  type: "github" #Oauth2 登录接入类型，github/gitlab/jihulab/gitee/gitea
  admin: "renyuanceshi" #管理员列表，填写自己github的用户名
  clientid: "nz_github_oauth_client_id" #填写前面github OAuth中申请的id
  clientsecret: "nz_github_oauth_client_secret"#填写前面github中申请的secret
  endpoint: "" # 如gitea自建需要设置
site:
  brand: "nz_site_title"
  cookiename: "nezha-dashboard" #浏览器 Cookie 字段名，可不改
  theme: "default"
ddns:
  enable: false
  provider: "webhook" # 如需使用多配置功能，请把此项留空
  accessid: ""
  accesssecret: ""
  webhookmethod: ""
  webhookurl: ""
  webhookrequestbody: ""
  webhookheaders: ""
  maxretries: 3
  profiles:
    example:
      provider: ""
      accessid: ""
      accesssecret: ""
      webhookmethod: ""
      webhookurl: ""
      webhookrequestbody: ""
      webhookheaders: ""
```

编辑好配置文件后，pm2 start /home/milaone/nezha/dashboard
```
pm2 start ~/nezhacmd/dashboard
pm2 save
```

截止到现在面板的部署部分已经完成，现在已经可以使用https://nezha.rycs.us.kg访问面板了。<br />

服务器启动后，访问面板，然后登录，进入到后台管理中，设置<br />
找到“未接入CDN的面板服务器域名/IP”选项<br />
填入用于连接agent的域名，该域名一定不要启用cdn*说3遍<br />
<br />
<br />

## Agent端（被管理的服务器-客户端）
### 直接下载作者官方二进制文件。一行命令连接服务器面板。
```
cd ~/nezhacmd
curl -L https://github.com/nezhahq/agent/releases/download/v0.18.18/nezha-agent_freebsd_amd64.zip -o agent.zip
unzip -j agent.zip
rm agent.zip
./nezha-agent -s nezha.rycs.us.kg:1159 -p sjOdhaemYFxXXOoirp -d 
```
 
## 贡献

|姓名|主页|内容|
| :------------: | :------------: | :------------: |
|米拉一 (Milaone Channel)|https://www.milaone.com/archives/101.html|SERV00中部署探针程序哪吒面板的过程及代码|

