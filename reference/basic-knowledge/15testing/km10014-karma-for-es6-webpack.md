# KM10014 - Karma搭建ES6语法单元测试环境

在前端开发中，测试通常被忽略，本文介绍如何搭建ES6语法基础上的Karma自动化测试框架。什么是Karma呢？参考：[KTS10105 - Karma Installing](/reference/3kts-works/31training/kts10105-karma-installing.md)，本文是安装Karma的升级版，主要是让Karma可支持ES6的语法，它是Google开发的。Karma本身会启动一个服务器，生成包含JS源代码和JS测试脚本的页面。——运行浏览器界面，并显示测试结果。

如果开启检测，则文件有修改，则执行以上过程。

## Karma安装配置

初始化项目结构

```
karma-example
| -- src
      | -- index.js
| -- test
| -- package.json
```

这里不关注`index.js`文件内容，只要记住它是源代码目录即可，内容可以很简单。接下来可以搭建Karma环境了，虽然这部分内容已经在[KTS10105 - Karma Installing](/reference/3kts-works/31training/kts10105-karma-installing.md) 有说明，但这里重复走一次，加深映像。一般搭建karma环境需要先初始化测试环境，所以可以全局安装`karma-cli`命令行

```bash
npm install -g karma-cli
```

全局安装过后，则可以直接在项目中安装karma包

```
npm install karma --save-dev
```

然后就可以在工程目录中使用`karma init`命令来初始化测试环境，按照[KTS10105](/reference/3kts-works/31training/kts10105-karma-installing.md)中的步骤来处理，在初始化过程中，使用jasmine测试框架，而运行环境使用PhantomJS，这个命令执行完成过后，项目根目录会生成`karma.conf.js`文件。

## Karma运行

在test目录中写一个简单的测试脚本，因为使用的是jasmine测试框架，具体api可参考jasmine api，内容如下：

```javascript
describe('index.js: ', function() { 
  it('isNum() should work fine.', function() { 
    expect(isNum(1)).toBe(true) 
    expect(isNum('1')).toBe(false) 
  }) 
})
```

然后在根目录运行karma start命令，则可以看到最终运行结果，因为这个过程中设置了监控文件的修改，如果修改了源文件或测试脚本时，Karma会自动帮我们再次运行，无需手动操作。

## Coverage

如何衡量测试脚本的质量呢？其中一个参考指标就是代码覆盖率（Coverage）。简单说就是测试中运行的代码占到总体代码的比率，其中又分为：行数覆盖率、分支覆盖率等。虽然并不是说代码覆盖率高，测试的脚本写得越好，但是代码覆盖率对撰写测试脚本还有一定指导意义，接下来指导如何在Karma配置文件中添加Coverage。

先在环境中安装Karma覆盖率工具：

```
npm install --save-dev karma-coverage
```

然后修改配置文件`karma.conf.js`如下：

```javascript
module.exports = function(config) { 
  config.set({ 
    basePath: '', 
    frameworks: ['jasmine'], 
    files: [ 
      'src/**/*.js', 
      'test/**/*.js' 
    ], 
    exclude: [], 

    // modified 
    preprocessors: { 
        'src/**/*.js': ['coverage'] 
    }, 

    //modified 
    reporters: ['progress', 'coverage'], 

    // add 
    coverageReporter: { 
      type : 'html', 
      dir : 'coverage/' 
    }, 

    port: 9876, 
    colors: true, 
    logLevel: config.LOG_INFO, 
    autoWatch: true, 
    browsers: ['PhantomJS'], 
    singleRun: false, 
    concurrency: Infinity 
  }) 
}
```

然后再运行karma start后，会在目录下生成coverage目录，里面有本次测试覆盖报告，如果遇到下边错：

```
19 04 2017 16:46:40.937:ERROR [preprocess]: Can not load "coverage", it is not registered!
  Perhaps you are missing some plugin?
```

则还需要添加下边内容：

```javascript
        plugins: [
            'karma-webpack',
            'karma-jasmine',
            'karma-coverage',            // 这一行是覆盖率专用，同样记得npm install --save-dev karma-coverage
            "karma-sourcemap-loader",
            'karma-phantomjs-launcher',
            'karma-babel-preprocessor'
        ]
```

## 使用Webpack + Babel

在实际项目中，有时候需要使用Webpack和ES6，所以接下来将Webpack和Babel集成进Karma环境中：





