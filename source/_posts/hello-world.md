title: 留档：GitHub＋hexo＋godaddy
categories: technology 
date: 2015-09-11 19:22
comments: true
tags: [环境搭建,计算机]
---
环境准备部分
* 安装Node，去[node官网](https://nodejs.org/)根据自己的机子选择合适的一路傻瓜安装就行了，node安装好了一般npm也集成在里面了。
* 安装git，我用的是git的客户端[SourceTree](https://www.sourcetreeapp.com/)，选择很多，怎样顺手怎样来。

github部分
[**github**](https://github.com/)是一个开源代码库以及版本控制系统。

* 申请一个GitHub的账号，路径点击上面链接。
* 建立项目的仓库，这里根据你希望搭建的博客形式分为了两种方式：
	1. 个人或者组织的，这种情况下，你建立的仓库必须与你的用户名保持一致。比如：atmos/atmos.github.io。而且在这个仓库下的master分支将作为内容被建立并且发布到GitHub Pages site。
	2. 项目的主页。在这种情况下，可以针对每个具体的项目仓库创建其相对的主页，分支gh-pages作为内容来建立和发布内容。它的访问url一般为username(orgname).github.io/projectname。
	3. 总结：如果是希望建立全局性(内容上)的网页建议用第一种方式，否则用第二种方式。详细对比可见[GitHub Help](https://help.github.com/articles/user-organization-and-project-pages/)。
* 添加SSH公匙到Account settings －》SSH Keys －》Add SSH Key(其实如果不用SSH连接是可以省略这一步的)，具体过程[点击我](https://help.github.com/articles/generating-ssh-keys/)。将的很详细，跟着走就可以了。
* 将此仓库克隆到本地(此处需要建立没有历史纪录和操作记录的新分支，所以最好新克隆)，然后创建gh-pages的分支并且将里面的内容都删除，此处不要提交修改。具体过程[点击我](https://help.github.com/articles/creating-project-pages-manually/)。

hexo部分
[**hexo**](https://hexo.io/zh-cn/)是一个快速，简洁高效的博客框架。

* 在命令行里输入`npm install hexo-cli -g`进行安装。这里可能会出现几个问题。
	* 需要权限，此时输入变为`sudo npm install hexo-cli -g`即可。
	* 卡node-gyp rebuild的问题，比如以下错误:
	
		~~~sh
		node-gyp rebuild
		gyp WARN install got an error, rolling back install
		gyp ERR! configure error 
		gyp ERR! stack Error: EPERM, utime '/Users/dirk/.node-gyp/0.12.4'
		gyp ERR! stack     at Error (native)
		gyp ERR! System Darwin 14.3.0
		gyp ERR! command "node" "/usr/local/lib/node_modules/npm/node_modules/node-gyp/bin/node-gyp.js" "rebuild"
		gyp ERR! cwd /usr/local/lib/node_modules/hexo-cli/node_modules/hexo-fs/node_modules/chokidar/node_modules/fsevents
		gyp ERR! node -v v0.12.4
		gyp ERR! node-gyp -v v1.0.3
		gyp ERR! not ok 
		npm WARN optional dep failed, continuing fsevents@0.3.6
		~~~

		原因就是GFW啦，所以要使用下国内镜像然后安装，如下：
		
		~~~
		$ npm install -g cnpm --registry=https://registry.npm.taobao.org #可能需要sudo
		$ cnpm install hexo-cli -g
		~~~
* 接着执行以下代码，主要就是初始化blog，如果没有指定文件夹则默认就在当下文件夹，然后切换到初始化后的文件夹安装依赖，最后开启本地服务器，此时访问本地http://localhost:4000/就可以了，命令如下：

	~~~
	hexo init blog
	cd blog
	npm install
	~~~
	但是在实际执行中可能会遇见一些问题，比如：

	~~~
		[Error: Module did not self-register.]
		{ [Error: Cannot find module './build/default/DTraceProviderBindings'] code: 'MODULE_NOT_FOUND' }
		{ [Error: Cannot find module './build/Debug/DTraceProviderBindings'] code: 'MODULE_NOT_FOUND' }
		ERROR Plugin load failed: hexo-generator-feed
		ReferenceError: hexo is not defined
	~~~
	解决方案：
	
	~~~
		npm install hexo-generator-feed --save
		npm install hexo --save
	~~~
* 以上只是部署到了本地，实际上我们需要部署到github上，所以需要去修改hexo的配置，主要修改deploy部分，修改如下,注意type不能写github：

	~~~
		deploy:
		  type: git
		  repository: https://github.com/<username>/<username>.github.io.git
		  branch: gh-pages
	~~~
	然后执行以下命令：
	
	~~~
		hexo g
		hexo deploy
	~~~
	实际中可能遇见的问题，比如：
	
	~~~
		{ [Error: Cannot find module './build/Release/DTraceProviderBindings'] code: 'MODULE_NOT_FOUND' }
		{ [Error: Cannot find module './build/default/DTraceProviderBindings'] code: 'MODULE_NOT_FOUND' }
		{ [Error: Cannot find module './build/Debug/DTraceProviderBindings'] code: 'MODULE_NOT_FOUND' }
	~~~
	解决方案：
	
	~~~
		npm install hexo-deployer-git --save
	~~~
	
主题安装
[**主题寻找地址**](https://github.com/hexojs/hexo/wiki/Themes)
安装方法也很简单，具体的每个主题下有，大概就是将他们克隆到本地的themes文件夹下，然后修改hexo的配置里的theme为希望的主题。

域名部署
* 我是之前在godaddy上买过一个域名。
* [Godaddy注册商域名修改DNS地址](https://support.dnspod.cn/Kb/showarticle/tsid/42)。
* 在DNSPod添加域名后进入控制台添加纪录，这边已经有两条记录了，此时再添加两条，一条是A纪录的host为@指向github的ip，这里是固定的192.30.252.153。另外一条CNAME纪录的host为www指向你的主页<username>.github.io(当然这里也可以通过dig命令获取具体的ip值)，同理，如果是项目主页可以添加二级目录指向<username>.github.io。
* 在具体的项目下添加一个文件CNAME。里面写上你希望对应的域名，然后提交。接着生成部署hexo就行。
* [更加详细内容参照](https://help.github.com/articles/about-custom-domains-for-github-pages-sites/)。

另外一些参考资料
* [资料一](http://ibruce.info/2013/11/22/hexo-your-blog/)
* [资料二](https://medium.com/@LovettLovett/github-pages-godaddy-f0318c2f25a)

	
