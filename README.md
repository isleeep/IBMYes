# 1. 注册IBM云账户

打开 https://cloud.ibm.com/registration

填邮箱、密码、个人信息（姓名和国家即可，我用的真实信息）



# 2. 创建CF应用程序

登录IBM云 

创建CF应用程序

https://cloud.ibm.com/catalog/starters/cloud-foundry

选择区域 达拉斯

套餐 256MB（免费的）


选择运行时 Go


应用程序名称 自己取一个，举例 vvps

主机名默认和应用程序名称一样，举例 vvps

域 us-south.cf.appdomain.cloud


点击“创建”（如果和别人重名是不能创建成功的）


# 3. 部署V2Ray

打开IBM云的Shell https://cloud.ibm.com/shell

刚创建的CF应用程序，Shell连接会比较慢，要稍微等一会儿。

注意：在这个窗口里 Ctrl+C 不是复制，需要使用鼠标右键菜单来复制粘贴。

一键安装命令

```bash
wget --no-check-certificate -O install.sh https://raw.githubusercontent.com/crazypeace/IBMYes/master/install.sh && chmod +x install.sh  && ./install.sh
```
需要输入你的应用程序名称，举例 vvps


其它需要输入的除非你懂，否则一律回车选择默认

最后你会得到一个vmess链接，粘到v2rayN里去，已经可以用了。只是你会发现比较慢。



# 4. Cloudflare 中转提速

登录/注册 Cloudflare https://www.cloudflare.com/

在主页面上点击 Workers


点击 “创建Worker”


将左边“脚本”部分默认的内容删掉，用以下内容填充（其中vvps要换成你自己的主机名）
```
addEventListener(
"fetch",event => {
let url=new URL(event.request.url);
url.hostname="vvps.us-south.cf.appdomain.cloud";
let request=new Request(url,event.request);
event. respondWith(
fetch(request)
)
}
)
```
点击右下角“保存并部署”


记下框中的网址，它是你的worker的网址。


修改v2rayN中的配置。
将地址修改为 cloudflare.com

将伪装域名修改为 你的worker的网址 


试试吧，速度明显变快了，youtube.com不会半天刷不出来了，看个720p 1080p还行。



# 5. Github Action 定期重启IBM CF应用程序

IBM CF是免费的，就会有一些限制。比如10天不操作就会关机。（光运行着是不够的）

登录/注册 Github 打开 https://github.com/crazypeace/IBMYes

点击右上角的 Fork


点击 Settings - Secrets


点击右边的 New Secret


Name填 IBM_APP_NAME

Value填 应用程序名称



同样的 New Secret，我们还要再建以下 Secret 
```
CF_USER_IBM     // IBM 用户名
CF_PASSWORD_IBM // IBM云的密码
CF_ORG_IBM // 组织名
CF_SPACE_IBM // 空间名
```
打开 https://cloud.ibm.com/account/cloud-foundry

箭头所指就是 CF_ORG_IBM  组织

点击组织名

下图中框起来的就是 CF_SPACE_IBM 空间


点击 .github/workflows

点击 ibm.yml


 点击右边的修改按钮


第9行的意思是，定时在每个周五的12点0分运行。
```
    - cron: '0 12 * * FRI'
```    
为了避免大家都在同一时间重启服务，我们修改为其它随便什么时间，举例 周三的8点47
```
    - cron: '47 8 * * WED'
```
点击右边的绿色提交按钮


确认


到此为止，一个速度还行、流量无限的梯子就搭好了。
