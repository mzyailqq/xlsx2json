xlsx2json
=========

## 作用
* 让excel表达复杂的json格式
* 将excel转成json
* 将json导入mongo数据库。

## 为什么要做这个项目？
开发游戏的时候，策划用的是excel，而我们的数据库是mongo。
我们的工作流是策划写excel，导出json，然后导入mongo数据库。

因为excel是二维的，无法表达mongo文档里面数组和subdocument等复杂结构。
按excel结构来设计mongo，会使mongo受限并无法使用某些特性。

有一个clojure项目 [excel-to-json ](https://github.com/mhaemmerle/excel-to-json) 可以完成这个功能。
但是不懂clojure表示压力很大而且有些功能不符合我们的需求。

基于以上原因，就搞了这个项目。某些想法也是借鉴了[excel-to-json ](https://github.com/mhaemmerle/excel-to-json)，在此表示感谢。

## 使用说明
需要将mongo的bin目录和nodejs安装目录放到环境变量下。
* 查看帮助：执行`node index.js -h` 查看使用帮助；
* 导出json：双击`export.bat` 即可将 `./excel/*.xlsx` 文件导出到 `./json` 下。
* json导入mongo：先要修改下`index.js`里数据库配置 `db`,
双击`import.bat`即可将./json下所有的json文件导入到数据库中。
collection的名字就是json文件的名字。
* 还支持命令行传参导入导出特定excel，具体使用 node `index.js --help` 查看。


示例1 test.xlsx(这是一个表格，排版原因，分成两行。)

| id   | desc        | flag   | otherid#[] | words#[]     | map#[]     |
| ---- | ----------- | ------ | ---------- | ------------ | ---------- |
| 123  | description | true   | 1,2        | 哈哈,呵呵    | true,true  |
| 456  | 描述        | false  | 3,5,8      | shit,my god  | false,true |


| data#{}    | hero#[{}]                     |
| ---------- | ----------------------------- |
| a:123;b:45 | id:2;level:30,id:3;level:80   |
| a:11;b:22  | id:9;level:38,id:17;level:100 |

输出如下：

```json
[{
    "id": 123,
    "desc": "description",
    "flag": true,
    "otherid": [1, 2],
    "words": ["哈哈", "呵呵"],
    "map": [true, true],
    "data": {
        "a": 123,
        "b": 45
    },
    "hero": [
      {"id": 2,"level": 30}, 
      {"id": 3,"level": 80}
    ]
}, {
    "id": 456,
    "desc": "描述",
    "flag": false,
    "otherid": [3, 5, 8],
    "words": ["shit", "my god"],
    "map": [false, true],
    "data": {
        "a": 11,
        "b": 22
    },
    "hero": [
      {"id": 9, "level": 38 }, 
      {"id": 17,"level": 100}
    ]
}]
```

## 支持以下数据类型
* number 数字类型
* boolean  布尔
* string 字符串
* object 对象  // 不支持对象内有数组以及对象嵌套对象，防止表格过度复杂。
* number-array  数字数组
* boolean-array  布尔数组
* string-array  字符串数组
* object-array 对象数组

## 表头规则
* 字段是基本数据类型(string,number,boolean)时候，不需要特殊设置。
* 字段是基本类型数组：此列表头的命名形式 `列名#[]` 。
* 字段是对象：此列表头的命名形式 `列名#{}` 。
* 字段是对象数组：此列表头的命名形式`列名#[{}]` 。

## 数据规则
* 关键符号都是半角符号。
* 数组使用逗号`,`分割。
* 对象属性使用分号`;`分割。

## 原理说明
* 依赖 `node-xlsx` 这个npm项目解析xlsx文件。
* 其实xlsx就是个zip文件，解压出来都是xml。
  有一个xml存的string，有相应个xml存的sheet。
  通过解析xml解析出excel数据(json格式)，这个就是`node-xlsx` 做的工作。
* 本项目只需利用 `node-xlsx` 解析xlsx文件，然后拼装自定的json数据。

## 补充
* 实验环境：win7_x64 + nodejs_v0.10.25
* 项目地址 [xlsx2json master](https://github.com/koalaylj/xlsx2json)
* 如有问题可以到QQ群内讨论：223460081
* 项目中的某些工具函数测试用例请参见我的gist js:validate & js:convert。
* 我们 mongo + excel 的项目会一直使用它，会一直维护。

## 项目当前进行的工作
* 添加测试用例。
* 招募contributor
* 接收好的建议