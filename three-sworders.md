
# Three sord

## Feature
| 命令 | 特点             | 场景                                                     |
|------|------------------|----------------------------------------------------------|
| grep | 过滤             | 过滤文件内容                                             |
| sed  | 替换、修改、取行 | 1.替换、修改文件内容<br> 2.去指定某一行                  |
| awk  | 取列、统计计算   | 1.取列<br> 2.对比，比较 >= <= != ><<br> 3.统计、计算数组 |

## 1.grep

## 2.sed
### 2.1 特点及格式
sed:stream editor for filtering and transforming text

sed格式
| 命令 | 选项 | 功能          | 文件名  | 备注                                  |
|------|------|---------------|---------|---------------------------------------|
| sed  | -r   | 's#old#new#g' | old.txt | s:表示替换<br> g:修饰符（不是必须的） |

- -r:使用扩展正则
- -n:取消默认输出

sed命令功能:增删改查
| 功能 | 描述                    |
|------|-------------------------|
| s    | 替换:substitute         |
| p    | 显示:print              |
| d    | 删除:delete(以行为单位) |
| cai  | 增加:c/a/i              |
### 2.2 执行过程
- 描述：找谁干啥
- 找谁：你要哪一行
- 干啥：增删改查

### 2.3 核心应用
#### 2.3.1 查找
| 查找格式           | 描述                             |
|--------------------|----------------------------------|
| '1p' '2p'          | 指定行号查找                     |
| '1,5p'             | 指定行号范围查找                 |
| '/lidao/p'         | 类似于grep过滤，//之间可以写正则 |
| '/10:00/,/11:00/p' | 表示范围的过滤                   |
| '1,/oldboy/p'      | 混合                             |


```shell
#指定行号输出
➜ ~/r/s/three-sord master ✗ sed -n '3p' sed-test.md 
3,sed-3

#默认输出(第三行会多输出一遍)
➜ ~/r/s/three-sord master ✗ sed '3p' sed-test.md  
1,sed-1
2,sed-2
3,sed-3
3,sed-3
4,sed-4
5,sed-5
6,sed-6
7,sed-7
8,sed-8
9,sed-9

```

```shell
#指定行号范围
➜ ~/r/s/three-sord master ✗ sed -n '1,5p' sed-test.md 
1,sed-1
2,sed-2
3,sed-3
4,sed-4
5,sed-5

#从第4行到最后一行($p 表示最后一行)
➜ ~/r/s/three-sord master ✗ sed -n '4,$p' sed-test.md 
4,sed-4
5,sed-5
6,sed-6
7,sed-7
8,sed-8
9,sed-9

➜ ~/r/s/three-sord master ✗ sed -n '$p' sed-test.md 
9,sed-9

```

过滤
```shell
➜ ~/r/s/three-sord master ✗ sed -n '/5/p' sed-test.md
5,sed-5

➜ ~/r/s/three-sord master ✗ sed -n '/[45]/p' sed-test.md
4,sed-4
5,sed-5

➜ ~/r/s/three-sord master ✗ sed -n '/[0-9]/p' sed-test.md
1,sed-1
2,sed-2
3,sed-3
4,sed-4
5,sed-5
6,sed-6
7,sed-7
8,sed-8
9,sed-9

sed -n '/3/,/5/p' sed-test.md 
3,sed-3
4,sed-4
5,sed-5

#过滤11:02:30的数据
sed -n '/11:02:30/p' sed-test.md

#过滤11:02:00 - 11:02:30 的数据,如果“11:02:30”是数据找不到就默认找到结尾
sed -n '/11:02:00/,/11:02:30/p' sed-test.md
```

1)表示范围过滤的时候，如果结尾内容匹配不到就是一直显示到最后一行
```shell
#15 这个数据找不到，就一直显示到最后一行
➜ ~/r/s/three-sord master ✗ sed -n '/3/,/15/p' sed-test.md
3,sed-3,girl
4,sed-4,boy
5,sed-5,girl
6,sed-6,girl
7,sed-7,girl
8,sed-8,boy
9,sed-9,boy
```
#### 2.3.2 sed 删除
| 查找格式           | 描述                                 |
|--------------------|--------------------------------------|
| '2d'               | 指定行号删除                         |
| '1,5d'             | 指定行号范围删除                     |
| '/lidao/d'         | 类似于grep过滤删除，//之间可以写正则 |
| '/10:00/,/11:00/d' | 表示范围的过滤删除                   |
| '1,/oldboy/d'      | 混合                                 |

```shell
➜ ~/r/s/three-sord master ✗ cat sed-test.md 
1,sed-1,boy
2,sed-2,boy
3,sed-3,girl
4,sed-4,boy
5,sed-5,girl
6,sed-6,girl
7,sed-7,girl
8,sed-8,boy
9,sed-9,boy

➜ ~/r/s/three-sord master ✗ sed '3d' sed-test.md 
1,sed-1,boy
2,sed-2,boy
4,sed-4,boy
5,sed-5,girl
6,sed-6,girl
7,sed-7,girl
8,sed-8,boy
9,sed-9,boy

➜ ~/r/s/three-sord master ✗ sed '2d' sed-test.md
1,sed-1,boy
3,sed-3,girl
4,sed-4,boy
5,sed-5,girl
6,sed-6,girl
7,sed-7,girl
8,sed-8,boy
9,sed-9,boy

➜ ~/r/s/three-sord master ✗ sed '2,3d' sed-test.md 
1,sed-1,boy
4,sed-4,boy
5,sed-5,girl
6,sed-6,girl
7,sed-7,girl
8,sed-8,boy
9,sed-9,boy
➜ ~/r/s/three
```

删除文件中的空行和包含"#"行
```shell
egrep -v '^$|#' /etc/ssh/sshd_config
sed -r '/^$|#/d' /etc/ssh/sshd_config 

# ! 的妙用:sed awk 中表示取反
sed -nr '/^$|#/!p' /etc/ssh/sshd_config
```

#### 2.3.3 sed 增删改
| 命令 | 描述                            |
|------|---------------------------------|
| c    | replace:替代指定行              |
| a    | append:向指定行的下一行追加内容 |
| i    | insert:向指定行的上一行插入内容 |

```shell
➜ ~/r/s/three-sord master ✗ cat sed-test.md 
1,sed-1,boy
2,sed-2,boy
3,sed-3,girl
4,sed-4,boy
5,sed-5,girl
6,sed-6,girl
7,sed-7,girl
8,sed-8,boy
9,sed-9,boy

➜ ~/r/s/three-sord master ✗ sed '3a 996,lidao996,ufo' sed-test.md
1,sed-1,boy
2,sed-2,boy
3,sed-3,girl
996,lidao996,ufo
4,sed-4,boy
5,sed-5,girl
6,sed-6,girl
7,sed-7,girl
8,sed-8,boy
9,sed-9,boy

➜ ~/r/s/three-sord master ✗ sed '3i 996,lidao996,ufo' sed-test.md
1,sed-1,boy
2,sed-2,boy
996,lidao996,ufo
3,sed-3,girl
4,sed-4,boy
5,sed-5,girl
6,sed-6,girl
7,sed-7,girl
8,sed-8,boy
9,sed-9,boy

➜ ~/r/s/three-sord master ✗ sed '3c 996,lidao996,ufo' sed-test.md
1,sed-1,boy
2,sed-2,boy
996,lidao996,ufo
4,sed-4,boy
5,sed-5,girl
6,sed-6,girl
7,sed-7,girl
8,sed-8,boy
9,sed-9,boy
```
向文件内容中追加多行内容
```shell
向config里面追加
1-
2*
3%
#方法1
cat >>config<<'EOF'
1-
2*
3%
EOF

#方法2
sed '$a 1-\n2*\n3%' config
```
#### 2.3.4 sed 替换
- s:substitute 替换
- 格式：'s###g' 's///g'
- g:global 全局替换,sed 替换每行所有匹配的内容，sed只默认替换每一行的第一个匹配的内容

```shell
➜ ~/r/s/three-sord master ✗ #将每一行的0-9的数字替换为空
➜ ~/r/s/three-sord master ✗ sed 's#[0-9]##g' sed-test.md
,sed-,boy
,sed-,boy
,sed-,girl
,sed-,boy
,sed-,girl
,sed-,girl
,sed-,girl
,sed-,boy
,sed-,boy
```
- 后向引用，反向引用
- 口诀：先保护再使用
```shell
➜ ~/r/s/three-sord master ✗ echo 123456 |sed -r 's#(.*)#<\1>#g'
<123456>

#练习
#1.echo oldboy_lidao 通过sed后向引用 显示为 lidao_oldboy
➜ ~/r/s/three-sord master ✗ echo oldboy_lidao | sed -r "s#^(.*)_(.*)#\2_\1#g"
lidao_oldboy

➜ ~/r/s/three-sord master ✗ echo oldboy_lidao | sed -r "s#^([a-z]+)_([a-z]+)#\2_\1#g"
lidao_oldboy

#2. ip a 通过反向引用取出eth0网卡ip
➜ ~/r/s/three-sord master ✗ ip a s eno1 | sed -n "4p" | sed -r "s#(^.*t )(.*)(/.*)#\2#g"
10.221.98.35
#精简
➜ ~/r/s/three-sord master ✗ ip a s eno1 |  sed -nr "4s#(^.*t )(.*)(/.*)#\2#gp"
10.221.98.35

#3 stat /etc/hosts 取出权限644
➜ ~/r/s/three-sord master ✗ stat /etc/hosts  | sed -nr "4s#(^.*\(0)(.*)(/-.*)#\2#gp"
644


```
## 3.awk
### 特点与应用场景
- 过滤、统计、计算
- 过滤、统计 日志

### 执行过程
awk -F, 'BEGIN{print "name"}{print $2}END{print "end of file"}' sed-test.md
```shell
➜ ~/r/s/three-sord master ✗ cat sed-test.md 
1,sed-1,boy
2,sed-2,boy
3,sed-3,girl
4,sed-4,boy
5,sed-5,girl
6,sed-6,girl
7,sed-7,girl
8,sed-8,boy
9,sed-9,boy

#无条件执行
➜ ~/r/s/three-sord master ✗ awk -F, 'BEGIN{print "name"}{print $2}END{print "end of file"}' sed-test.md
name
sed-1
sed-2
sed-3
sed-4
sed-5
sed-6
sed-7
sed-8
sed-9
end of file

#带条件执行
➜ ~/r/s/three-sord master ✗ awk -F, 'BEGIN{print "name"}NR==1{print $2}END{print "end of file"}' sed-test.md
name
sed-1
end of file

➜ ~/r/s/three-sord master ✗ awk -F, 'BEGIN{print "name"}NR==2{print $2}END{print "end of file"}' sed-test.md
name
sed-2
end of file

➜ ~/r/s/three-sord master ✗ awk -F, 'BEGIN{print "name"}NR==3{print $2}END{print "end of file"}' sed-test.md
name
sed-3
end of file
```

awk :
- 1.awk读取文件之前：命令行复制或命令行参数 -> BEGIN{}
- 2.awk读取文件：找谁{干啥} -> 条件{动作} (或者可以这么理解：读取文件内容->判断->执行对应命令->读取下一行)
- 3.awk读取完文件：END{}

### 行与列
| 名词                               | awk中说法      | 说明                   |
|------------------------------------|----------------|------------------------|
| 行                                 | 记录 record    | 每行默认通过回车分割的 |
| 列                                 | 字段，域 filed | 每列默认通过空格分割的 |
| awk 中和行，结束标记都是可以修改的 |                |                        |

- 1. **awk取行**

| awk            |                 |
|----------------|-----------------|
| NR==1          | 取出某一行      |
| NR>=1 && NR<=5 | 取出1到5行      |
| /oldboy/       |                 |
| /101/,/105/    | 取出101-105行   |
| 符号           | > < >= <= == != |

```shell
#找谁{干啥}
#条件{动作}

➜ ~/r/s/three-sord master ✗ awk 'NR==1' sed-test.md 
1,sed-1,boy
➜ ~/r/s/three-sord master ✗ awk 'NR>=1 && NR<=5' sed-test.md

1,sed-1,boy
2,sed-2,boy
3,sed-3,girl
4,sed-4,boy
5,sed-5,girl

➜ ~/r/s/three-sord master ✗ awk '/boy/' sed-test.md
1,sed-1,boy
2,sed-2,boy
4,sed-4,boy
8,sed-8,boy
9,sed-9,boy
➜ ~/r/s/three-sord master ✗ awk '/1/,/5/' sed-test.md

1,sed-1,boy
2,sed-2,boy
3,sed-3,girl
4,sed-4,boy
5,sed-5,girl
```

- 2. **awk取列**

| 字符  | 含义                                                          |
|-------|---------------------------------------------------------------|
| -F    | 指定分隔符，指定每一列结束标记(默认是空格，连续的空格，tab键) |
| $数字 | 取出某一列, 在awk中$表示取出某一列                            |
| $0    | 表示取出整行内容                                              |



```shell
➜ ~/r/s/three-sord master ✗ ls -l                        
总用量 20
-rw-rw-r-- 1 mi mi    9 2月   7 16:40 config
-rw-rw-r-- 1 mi mi  112 2月   7 15:16 sed-test.md
-rw-rw-r-- 1 mi mi 9549 2月   8 12:11 three-sworders.md

➜ ~/r/s/three-sord master ✗ ls -l | awk '{print $8}'     

16:40
15:16
12:11

➜ ~/r/s/three-sord master ✗ ls -l | awk 'NR==2{print $0}'
-rw-rw-r-- 1 mi mi    9 2月   7 16:40 config

➜ ~/r/s/three-sord master ✗ ls -l | awk '{print $5,$9}' | column -t
9      config
112    sed-test.md
10427  three-sworders.md

#取出/etc/passwd 第一列和最后一列
➜ ~/r/s/three-sord master ✗ awk -F: '{print $1,$NF}' /etc/passwd | column -t
root                 /bin/zsh
daemon               /usr/sbin/nologin
bin                  /usr/sbin/nologin
sys                  /usr/sbin/nologin
sync                 /bin/sync
games                /usr/sbin/nologin

#在打印的两列中添加一些符号
➜ ~/r/s/three-sord master ✗ awk -F: '{print $1 "@@" $NF}' /etc/passwd 
root@@/bin/zsh 
daemon@@/usr/sbin/nologin
bin@@/usr/sbin/nologin
sys@@/usr/sbin/nologi
sync@@/bin/sync
games@@/usr/sbin/nologin

#使用awk调换passwd 第1列和最后一列内容，然后显示调换后的每一行内容

➜ ~/r/s/three-sord master ✗ awk -F: '{print $NF,$2,$3,$4,$5,$6,$1}' /etc/passwd
/bin/zsh x 0 0 root /root root
/usr/sbin/nologin x 1 1 daemon /usr/sbin daemon
/usr/sbin/nologin x 2 2 bin /bin bin
/usr/sbin/nologin x 3 3 sys /dev sys

➜ ~/r/s/three-sord master ✗ awk -F: '{print $NF":"$2":"$3":"$4":"$5":"$6":"$1}' /etc/passwd
/bin/zsh:x:0:0:root:/root:root
/usr/sbin/nologin:x:1:1:daemon:/usr/sbin:daemon
/usr/sbin/nologin:x:2:2:bin:/bin:bin
/usr/sbin/nologin:x:3:3:sys:/dev:sys
/bin/sync:x:4:65534:sync:/bin:sync

➜ ~/r/s/three-sord master ✗ awk -F: -vOFS=: '{print $NF,$2,$3,$4,$5,$6,$1}' /etc/passwd
/bin/zsh:x:0:0:root:/root:root
/usr/sbin/nologin:x:1:1:daemon:/usr/sbin:daemon
/usr/sbin/nologin:x:2:2:bin:/bin:bin
/usr/sbin/nologin:x:3:3:sys:/dev:sys
/bin/sync:x:4:65534:sync:/bin:sync

#取出网卡ip地址 -F 是可以支持正则的
➜ ~/r/s/three-sord master ✗ ip a s eno1                                  
2: eno1: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether c8:d9:d2:2b:94:94 brd ff:ff:ff:ff:ff:ff
    altname enp0s31f6
    inet 10.221.98.35/19 brd 10.221.127.255 scope global dynamic noprefixroute eno1
       valid_lft 74258sec preferred_lft 74258sec
    inet6 2408:8607:1b00:8:cad9:d2ff:fe2b:9494/128 scope global dynamic noprefixroute 
       valid_lft 2579861sec preferred_lft 592661sec
    inet6 fe80::16d7:e880:5827:e8fb/64 scope link noprefixroute 
       valid_lft forever preferred_lft forever

➜ ~/r/s/three-sord master ✗ ip a s eno1 | awk -F"[ /]+" 'NR==4{print $3}'
10.221.98.35

```
- awk内置变量

| 内置变量 |                                                                                    |
|----------|------------------------------------------------------------------------------------|
| NR       | Number of Record 记录号，行号                                                      |
| NF       | Number of Filed 每行有多少字段(列)，$NF:表示最后一列                               |
| FS       | -F: ====== -v FS=:Filed Separator 字段分隔符，每个字段结束标记                     |
| OFS      | Output Filed Separator 输出字段分隔符（awk显示每一列之间通过什么分隔，默认是空格） |

### 匹配模式
- 比较符号
- 正则
- 范围 表达式
- 特殊条件：BEGIN 和 END

1) 比较符号
2) 正则：
	- //支持扩展正则
	- awk 可以精确到某一列中包含/不包含某个内容
	- ~:包含
	- !~:不包含

        | 正则            | awk正则                   |
        |-----------------|---------------------------|
        | ^:表示以...开头 | 某一列的开头 $3~/^oldboy/ |
        | $:表示以...结尾 | 某一列的结尾 $4~/lidao$/  |
        | ^$:表示空行     | 某一列是空的              |

	```shell
	➜ ~/r/s/three-sord master ✗ awk -F: '$3~/^2/' /etc/passwd
	bin:x:2:2:bin:/bin:/usr/sbin/nologin

	#找出 第3列 以2开头的行 并显示第1列 第3列 和最后一列
	➜ ~/r/s/three-sord master ✗ awk -F: '$3~/^2/{print $1,$3,$NF}' /etc/passwd   
	bin 2 /usr/sbin/nologin
	➜ ~/r/s/three-sord master ✗ 
	
	#找出 第3列 以2或者3开头的行 并显示第1列 第3列 和最后一列
	➜ ~/r/s/three-sord master ✗ awk -F: '$3~/^[23]/{print $1,$3,$NF}' /etc/passwd | column -t
	bin       2   /usr/sbin/nologin
	sys       3   /usr/sbin/nologin
	www-data  33  /usr/sbin/nologin
	backup    34  /usr/sbin/nologin
	list      38  /usr/sbin/nologin
	irc       39  /usr/sbin/nologin
	
	#找出 第3列 以2开头或者包含3的行 并显示第1列 第3列 和最后一列
	➜ ~/r/s/three-sord master ✗ awk -F: '$3~/^2|3/{print $1,$3,$NF}' /etc/passwd | column -t
	bin             2      /usr/sbin/nologin
	sys             3      /usr/sbin/nologin
	proxy           13     /usr/sbin/nologin
	www-data        33     /usr/sbin/nologin
	backup          34     /usr/sbin/nologin
	list            38     /usr/sbin/nologin
	irc             39     /usr/sbin/nologin
	nobody          65534  /usr/sbin/nologin
	messagebus      103    /usr/sbin/nologin
	cups-pk-helper  113    /usr/sbin/nologin
	pulse           123    /usr/sbin/nologin
	
	```

3) 表示范围
	- /从哪里开始/,/到哪里结束/ 常用
	- NR==1,NR==5 从第1行开始，到第5行结束，类似与sed -n '1,5p'

	```shell
	➜ ~/r/s/three-sord master ✗ cat sed-test.md
	1,sed-1,boy
	2,sed-2,boy
	3,sed-3,girl
	4,sed-4,boy
	5,sed-5,girl
	6,sed-6,girl
	7,sed-7,girl
	8,sed-8,boy
	9,sed-9,boy
	
	#显示指定范围内的性别
	➜ ~/r/s/three-sord master ✗ awk -F, '/2/,/3/{print $NF}' sed-test.md
	boy
	girl
	
	```

4) 特殊模式:BEGIN{} 和 END{}
	| 模式    | 含义                  | 应哟场景                                                                                                      |
	|---------|-----------------------|---------------------------------------------------------------------------------------------------------------|
	| BEGIN{} | 在awk读取文件之前执行 | 1)进行简单的统计、计算不涉及读取文件<br> 2)用来处理文件之前，先加表头<br> 3)用来定义awk变量(很少用，可以用-v) |
	| END{}   | 在awk读取文件之后执行 | 1)awk进行统计，一般:先进行计算，END里面输出结果<br>2)awk 使用数组，用来输出数组结果                           |
	
	- END{} 统计计算
	- 统计方法：

	| 统计方法              |             |                |
	|-----------------------|-------------|----------------|
	| i=i+1                 | i++         | 计算，统计次数 |
	| sum=sum+???           | sum+=???    | 求和 累计      |
	| array[$N]=array[$N]+1 | array[$N]++ | 数组分类计数   |

	```shell
	#统计/etc/services 中空行数目
	➜ ~/r/s/three-sord master ✗ awk '/^$/{i++}END{print i}' /etc/services 
	6
	
	➜ ~/r/s/three-sord master ✗ awk '/^$/' /etc/services | wc -l
	6
	
	#计算1-10的和
	➜ ~/r/s/three-sord master ✗ seq 10                      
	1
	2
	3
	4
	5
	6
	7
	8
	9
	10
	➜ ~/r/s/three-sord master ✗ 
	➜ ~/r/s/three-sord master ✗ 
	➜ ~/r/s/three-sord master ✗ 
	➜ ~/r/s/three-sord master ✗ seq 10 | awk '{sum+=$0}END{print sum}'
	55
	
	#显示加的过程
	➜ ~/r/s/three-sord master ✗ seq 10 | awk '{sum+=$0;print sum}END{print sum}'
	1
	3
	6
	10
	15
	21
	28
	36
	45
	55
	55
	➜ ~/r/s/three-sord master ✗
	```
#### awk数组
- 统计日志：类似于统计 每个ip出现次数，统计每种状态码出现次数，统计系统中每个用户被攻击的次数，统计攻击者ip出现次数
- 统计求和：统计每个ip的流量

|                  | shell数组                                        | awk数组                        |                                                                |
|------------------|--------------------------------------------------|--------------------------------|----------------------------------------------------------------|
| 形式             | array[0]=oldboy array[1]=lidao                   | array[0]=oldboy array[1]=lidao |                                                                |
| 使用             | echo \${array[0]} \${array[1]}                   | print array[0] array[1]        |                                                                |
| 批量输出数组内容 | for i in \${array[*]} <br>do<br>echo \$i<br>done | for (i in array)<br> print i   | awk 数组专用循环，变量获取到的是数组下标，array[i]才是数组内容 |

```shell
#awk 中 字母 会识别为变量，如果只是想用字符串，需要使用双引号引起来
➜ ~/r/s/three-sord master ✗ awk 'BEGIN{a[0]=oldboy;a[1]=lidao; print a[0],a[1]}'
 
➜ ~/r/s/three-sord master ✗ awk 'BEGIN{a[0]=12306;a[1]=lidao; print a[0],a[1]}'
12306 
➜ ~/r/s/three-sord master ✗ awk 'BEGIN{a[0]=12306;a[1]="lidao"; print a[0],a[1]}'
12306 lidao

#批量输出数组内容：为啥数字不是顺序的
➜ ~/r/s/three-sord master ✗ awk 'BEGIN{a[0]=12306; a[1]=123; for(i in a) print i}'       
1
0
➜ ~/r/s/three-sord master ✗ awk 'BEGIN{a[0]=12306; a[1]="lidao"; for(i in a) print a[i]}'
lidao
12306

```
- 案例:统计sed-test.md中boy和girl出现的次数
```shell
➜ ~/r/s/three-sord master ✗ awk -F, '{array[$3]++}END{for(i in array) print i,array[i]}' sed-test.md 
boy 5
girl 4
#array[]++: 你要统计什么[]里面就是什么（某一列）
#本例中 $3 就是性别
```

### for 循环
| shell                                         | awk                           |
|-----------------------------------------------|-------------------------------|
| for((i=1;i<=10;i++))<br>do<br>echo $i<br>done | for(i=1;i<=10;i++)<br>print i |

```shell
#1+...+10
➜ ~/r/s/three-sord master ✗ awk 'BEGIN{for(i=1;i<=10;i++)sum+=i; print sum}'    
55

#体现循环过程
➜ ~/r/s/three-sord master ✗ awk 'BEGIN{for(i=1;i<=10;i++){sum+=i; print sum}}'
1
3
6
10
15
21
28
36
45
55
```

### if 判断
if(条件){print "1";print"2"}
if(条件){print "1";print"2"} else {print "1";print"2"}
```shell
#统计磁盘空间不足
➜ ~/r/s/three-sord master ✗ df -h | awk -F"[ %]+" 'NR>1{if($5>70)print $1, "disk not enough";} '
/dev/nvme0n1p2 disk not enough
/dev/loop0 disk not enough
/dev/loop1 disk not enough
/dev/loop2 disk not enough
/dev/loop3 disk not enough
/dev/loop4 disk not enough

#注意:awk使用多个条件的时候， 第一个条件可以放在‘条件{动作}‘ 第二个条件一般放在if
```
- 统计单词中个数小于6的个数
```shell
➜ ~/r/s/three-sord master ✗ echo I am oldboy teacher welcom to oldboy training class. | awk '{for(i=1;i<=NF;i++){if(length($i)<6){sum++;print $i}}}END{print sum}'
I
am
to
3

# length 是 awk内置函数
➜ ~/r/s/three-sord master ✗ echo I am oldboy teacher welcom to oldboy training class. | awk '{print length()}'                                                    
52
➜ ~/r/s/three-sord master ✗ echo I am oldboy teacher welcom to oldboy training class. | awk '{print length($0)}'
52
➜ ~/r/s/three-sord master ✗ echo I am oldboy teacher welcom to oldboy training class. | awk '{print length($1)}'
1
➜ ~/r/s/three-sord master ✗ echo I am oldboy teacher welcom to oldboy training class. | awk '{print length($2)}'
2
➜ ~/r/s/three-sord master ✗ echo I am oldboy teacher welcom to oldboy training class. | awk '{print length($3)}'
6
➜ ~/r/s/three-sord master ✗
```

