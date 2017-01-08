title: yeoman建立脚手架
categories: technology 
date: 2015-11-03 22:10
comments: true
tags: [前端,计算机]
---

* 首先你要先安装yeoman，`npm install -g yo bower grunt-cli gulp`。
* generator是一个Node.js的模块。
* 建立一个写generator的文件，必须命名为generator-name(name是自己取)。比如我建立一个generator-lulutia-react。
* `npm init`来建立一个package.json。

    ```
    {
  "name": "generator-lulutia-react",//必须generator开头
  "version": "0.0.1",
  "description": "the bone to build a app",
  "files": [
    "app",
    "router"
  ],//必须是你的generator用到的文件构成的数组
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "keywords": [
    "yeoman-generator"//必须有这条
  ],
  "author": "lulutia",
  "license": "MIT"
}
    ```

* `npm install yeoman-generator --save`//yeoman的依赖必须是最新的
* 默认执行`yo name`时执行的是app内的内容，`yo name:subcommand`执行的是其他文件夹里的内容。
* yeoman提供两种组织方式来，其一是:`./`,其二是:`generators/`，注意第二种方式的package.json内的files要相应修改。
* 根据提供的基本结构来写自己的脚手架内容

    ```
var generators = require('yeoman-generator');

module.exports = yeoman.generators.Base.extend(
{
// The name `constructor` is important here
constructor: function () {
// Calling the super constructor is important so our generator is correctly set up
generators.Base.apply(this, arguments);

// Next, add your custom code
this.option('coffee'); // This method adds support for a `--coffee` flag
}
});
    ```

* 我这里用了几个库，Prompts:主要掌管generator与用户的交互

    ```
module.exports = yeoman.generators.Base.extend({
prompting: function () {
var done = this.async();
this.prompt({
  type    : 'input',
  name    : 'name',
  message : 'Your project name',
  default : this.appname // Default to current folder name
}, function (answers) {
  this.log(answers.name);
  done();
}.bind(this));
}
})
    ```

* writing里面用mkdirp建立基本骨架。在app内部建立templates文件夹，通过里面的文件的拷贝建立结构。

    ```
 mkdirp("assets/style");
 mkdirp("build");
    
   this.fs.copy(
           this.templatePath('coffeelint.json'),
           this.destinationPath('coffeelint.json')
         );
    ```

* `generator.installDependencies() `安装依赖.
* 在generator-name/文件夹下运行`npm link`可以使generator在本地可用。

    
意料之外的问题
* yeoman安装的时候表示node和npm的版本都落后了，然后npm这个命令就无法用了……简直了＝＝然后想用brew安装下，结果忘记brew没更新，各种更新无效。
* 直接用`curl -L https://www.npmjs.com/install.sh | sh`加上sudo也有问题，还是报权限错误。
* 决定可以用bower，但强迫症患者觉得这样不完美。可以直接重新下node，但觉得代价太大。
* 用了比较粗暴的方法，去npm 的github上下载了sh，然后`sudo sh /Users/muriel/Downloads/install.sh`来安装。完了顺便把brew更新下,把没升级的软件升级了`brew update;brew upgrade;brew cleanup`。
* 然后又出来一个问题，`npm install`按照package.json来安装但是目录结构有问题，原来npm的3开始决定采用扁平化处理，所以之前的代码为了适应这个改变必须要做一些改变。


[具体代码](https://github.com/lulutia/generator-lulutia-react)
