
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

-r:使用扩展正则
-n:取消默认输出

sed命令功能:增删改查
| 功能 | 描述                    |
|------|-------------------------|
| s    | 替换:substitute         |
| p    | 显示:print              |
| d    | 删除:delete(以行为单位) |
| cai  | 增加:c/a/i              |
### 2.2 执行过程
描述：找谁干啥
找谁：你要哪一行
干啥：增删改查

### 2.3 核心应用
#### 2.3.1 查找
| 查找格式           | 描述                             |
|--------------------|----------------------------------|
| '1p' '2p'          | 指定行号查找                     |
| '1,5p'             | 指定行号范围查找                 |
| '/lidao/p'         | 类似于grep过滤，//之间可以写正则 |
| '/10:00/,/11:00/p' | 表示范围的过滤                   |
| '1,/oldboy/p'      | 混合                             |


```
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

```
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
```
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
```
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

```
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
```
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

```
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
```
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
s:substitute 替换
格式：'s###g' 's///g'
g:global 全局替换,sed 替换每行所有匹配的内容，sed只默认替换每一行的第一个匹配的内容

```
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
后向引用，反向引用
口诀：先保护再使用
```
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

