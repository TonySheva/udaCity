#  man xxx 可以知道这个命令的详细操作,

#  比如 man ls 则可以看到ls -l  ls -a  等等的解释。

#  这个跟  --help 感觉差不多

#  像 rm, mv, cp 这样的短命令,而不是remove，move这样因为bit数越少会越好。

#  具体用法简单 rm 文件名 路径 ,  mv 文件名 路径 

#  . 是当前位置 , pwd 显示当前位置 常见可以../就是上一级目录

#  用*xx 可以模糊查询 带有xx的,注意*xx* 就是夹在中间的。 
#  若为Tony* 这样子的，则Tony一定在前面，为一个词。

#  想一次模糊查询jpg，png等文件,可以用  *{jpg,png,xxx}

#  也可以用？来进行模糊查询 比如说 pass?d 则会找到类似于 passed passad .. 

#  echo ?   这样会输出当前路径下一个字符的文件  如果？？ 那就两个..

# netstat -plten |grep 3000

#tcp6       0      0 :::3000                 :::*                    LISTEN      0          620834      2524/main

#kill -9 2524

# vim /etc/hosts 

#127.0.0.1  test.test.com

