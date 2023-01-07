[ref](https://juejin.cn/post/7184048390399852599)

fly.io 是一个容器化的部署平台，只需要一个Dockerfile文件就能部署代码到fly.io 的服务器上，同时还自动生成域名。此外其他的好处也很多，我根据自己体验，总结成了下面的这些条：

-   有免费使用的额度。不填写信用卡信息可以创建一个App，完全不收费；填写信用卡信息后每月有一定额度的免费流量，超过额度会额外收费。所以想做个小demo完全可以不填信用卡试用。
-   自动生成域名。比如你创建一个名字叫my_demo的App，那么部署完成后，就会生成my_demo.fly.dev的域名，可以全球访问，不用自己单独买域名了。
-   可以 SSH 连接进入服务器。部署完成后，可以通过flyctl ssh console 命令登录部署的服务器，所以相当于你有了一台免费的VPS，可以做你想做的任何事情。
-   部署简单，采用flyctl 命令集合统一部署;支持各种语言的各种框架来搭建部署环境，能自动识别当前目录下代码所采用的是哪个框架，自动部署。

  
# flyctl 命令

-   查看App状态: flyctl status
-   查看App信息: flyctl info
-   查看App列表: flyctl apps list
-   查看App的IP: flyctl ips list
-   销毁某个App: flyctl apps destroy



# [网页数据库配置](https://irithys.com/p/obsidian-livesync/#%E7%BD%91%E9%A1%B5%E6%95%B0%E6%8D%AE%E5%BA%93%E9%85%8D%E7%BD%AE)
