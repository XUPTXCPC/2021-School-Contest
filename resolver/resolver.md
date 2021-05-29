# 滚榜

## 官网

https://tools.icpc.global/resolver/

## Github地址

https://github.com/icpctools/icpctools

## 版本

resolver-2.3.491

## 使用方法

### 解压

解压resolver-2.3.491.zip（在该目录下可下载）

### 安装java环境

Mac下（其他类似）：

```bash
brew install oracle-jdk
```

### 导出json

1. 在Linux/Mac环境下使用终端打开awards.sh，Windows下打开awards.bat

```bash
./awards.sh
```

2. 在REST中输入比赛对应的URL、User和Password。

```
URL: http://{{domjudge地址}}/api/contests/{{比赛ID}}
User: admin
Password: admin的密码
```

3. 点击Connect。

4. 保存json文件

### 修改json

由于在awards中只能设置金银铜数量各为10个，很可能不够，因此需要手动修改json来设置金银铜的队伍，在json文件末尾的team_ids中增加或减少获奖队伍的id，如下：

```json
{"type":"awards","id":"icpc5109","op":"create","data":{"id":"gold-medal","citation":"Gold Medalist","team_ids":["id1","id2","id2"]}}
{"type":"awards","id":"icpc5110","op":"create","data":{"id":"silver-medal","citation":"Silver Medalist","team_ids":["id1","id2","id2"]}}
{"type":"awards","id":"icpc5111","op":"create","data":{"id":"bronze-medal","citation":"Bronze Medalist","team_ids":["id1","id2","id2"]}}
```

在该目录下有已经修改过的json文件(test.json)，供参考。

### 滚榜

在终端中使用resolver.sh/resolver.bat进行滚榜。

1. 中文支持

由于滚榜原生不支持中文，需要设置参数使用特定的字体，此处选择微软雅黑。

```bash
export ICPC_FONT="Microsoft YaHei"
```

2. 测试

在contest没有finalize之前，如果需要滚榜则需要加--test参数，否则会报错，如果finalize之后，则不需要加--test参数，否则也会报错。

```bash
./resolver.sh {{上一步保存的文件}}.json --test
```

example:

```bash
export ICPC_FONT="Microsoft YaHei" && ./resolver.sh test.json --test
```

## 踩坑

### 日志

每次滚榜的时候都会产生日志，产生的日志就在./log目录下，在每次运行resolver时会显示，遇到问题记得先查看日志。在github上可以下载源码，通过日志和源码可以定位问题所在位置。

### 脏数据

在没有finalize之前，可能会存在unjudge的提交，不解决这些数据在进行滚榜的时候会报错。

查看日志可以发现在下面这行之后出错，原因是存在unjudge的提交，无法处理。

```
I Removing n invalid submissions
```

解决方法：

点击finalize可以发现无法finalize，会提示unjudge的提交，将其解决后即可。

### 出现多余队伍

滚榜时出现并不存在的队伍，查看日志，发现程序在resolving row的时候挂了，由于比赛共有73支队，正确的应该是从72一直到0，分别resolving每一行。而实际上确是从73开始，这样resolving的有74支队。

```
I Resolving row: 73
```

查看json文件，发现id为1的队伍不应该出现在json中（可能是bug？），于是在json文件中删除本不应该存在的队伍，如以下两行：

```json
{"type":"organizations","id":"icpc34","op":"create","data":{"id":"1","icpc_id":"1","name":"UU","formal_name":"Utrecht University","country":"NLD"}}
{"type":"teams","id":"icpc108","op":"create","data":{"id":"1","name":"DOMjudge","group_ids":["1"]}}
```

然后进行滚榜即可。

### 

