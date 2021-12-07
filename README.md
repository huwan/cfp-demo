# 会议征稿倒计时网站

> 网站可以展示自己比较关心的一些学术会议的截稿日期等信息，也能显示倒计时。网站分成几个部分，最开始是以时间线的形式排列了所有的会议。接下来是**Featured CFPs**，重点关注对象，显示的倒计时可以精确到秒。后面是三类会议征稿，第一类**Upcoming CFPs**是还在征稿的会议，按照倒数日期排序，最近的在最上面；接下来是**Inactive CFPs**，即征稿日期已经过去了，但会议还未开始或结束的会议，按照距离开会日期远近排序，最近的在最上面；最后是**Past CFPs**，也就是正式的会议都已经结束了，按照结束日期排序，最近结束的在上面。
> 

<aside>
⚠️ 网站上面的会议信息，包括会议名称、链接、开会日期、deadline信息需要自己手动查找并更新。时间线排序、倒计时以及将会议分到Upcoming、Inactive和Past部分是可以自动完成的。

</aside>

网站是使用Hugo ([https://gohugo.io/](https://gohugo.io/)) 来生成静态网站。部署时有两种选择，一种是云端部署到GitHub上，并使用GitHub Action来自动生成、更新网页。另一种方式是本地部署到自己服务器上，需要多一些手动生成网站，配置cron来更新网站的步骤。

# 方案一：云端部署（推荐）

<aside>
💡 这种方式，只需要编辑config.toml配置文件，使用Git命令提交并推送到GitHub后，GitHub Action会自动运行，自己下载hugo来生成网站并更新gh-pages这个分支。

</aside>

Fork https://github.com/huwan/cfp-demo 到自己的GitHub账号。

Fork 完之后可以到设置中按照需要修改成repository名字（可选）。

在代码设置的Pages页面确保启用了GitHub Pages功能，并将Source选择gh-pages分支。如果出现下图绿色的网址就说明一切正常，直接访问网址即可看到部署后的网站。如果有自己的域名，可以添加并配置自己的域名。[具体参考网站提示操作即可](https://docs.github.com/en/pages/configuring-a-custom-domain-for-your-github-pages-site)。


之后的过程就是更新自己关注的会议列表。更新master分支的config.toml文件。按照需要在线编辑或者本地编辑后push到GitHub都行。

每一个会议都有下面字段：

- enable字段有三个值，0代表网页上不显示此会议，1是正常显示，2是高亮显示。在网站最上面**Featured CFPs**那一部分。注意，只能有一个会议此字段设置为2。不然倒计时会有问题（网站上其它会议的倒计时都是以天为级别的，Feature CFP会显示倒数的时分秒）。
- name字段是会议名称
- rank字段是CCF推荐国际学术会议的分类
- site是会议网站链接
- open是会议开始日期
- close是会议结束日期
- absddl是提交abstract的日期，如果没有，可以写N/A。不能写其它，否则会报错（因为网页最上面的timeline时需要特别处理）
- submddl是正式的截稿时间
- notidue是会议录用结果的日期

上面几项涉及到日期和时间的地方，具体格式都是`2006-01-02 15:04:05 -07:00`这种格式。年-月-日 时:分:秒，以及最后相对于UTC时区的时间差（范围是-12到+14，需要自己根据会议网站的说明自己转换一下。）需要注意的是，时间日期格式中每一项都是2位数字。下面示例显示了几种日期时间格式的具体使用情况，可以只写日期，也可与只写写日期和时间，或者如果需要很精确的话，日期、时间和时区都写齐全。

```bash
[[params.cfp.list]]
enable = 1
name = "FAST"
rank = "A"
site = "https://www.usenix.org/conference/fast22/call-for-papers"
open = "2022-02-21"
close = "2022-02-24 23:59:59"
absddl = "N/A"
submddl = "2021-09-23 23:59:59 -07:00"
notidue = "2021-12-10"
```

更新完之后，稍等部署完之后，GitHub提交的信息一栏会显示绿色的对号，显示成功。也可以进去上面的Actions标签页看具体的部署信息。如果是红色的叉号，就说明改动出现问题了，可以点红色的叉号跳转到Actions里面看具体错误信息。

# 方案二：本地部署

<aside>
💡 这种方式需要自己下载安装hugo（静态网页生成器）、生成网站、部署到服务器，并在服务器上设置cron job来周期性的更新网页上面会议属于那个列表。

</aside>


[安装 Hugo](https://gohugo.io/getting-started/installing)
参考下面网站使用包（软件）管理器自动安装，或者去GitHub上面下载已经编译好的可执行文件：[Releases · gohugoio/hugo](https://github.com/gohugoio/hugo/releases)

Clone代码到本地，将生成的

```bash
git clone https://github.com/huwan/cfp-demo.git
```

修改里面的config.toml。具体字段的解释见上面方案一的部分。

修改完之后，在cfp-demo目录下，也就是config所在的目录下面执行hugo命令就可以了。会自动生成一个public文件夹。将这个文件夹里面的所有内容放到自己网站的服务器上面去。

这时网站上会议会根据生成时的时间来分别放到Upcoming、Inactive和Past部分。可以设置cron 定时任务，每个小时执行一下hugo，并自动更新public到网站服务器上去。cron的设置可以参考
[Cron介绍](https://www.jianshu.com/p/51ea7bbbc54e)。
