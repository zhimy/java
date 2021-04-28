$grep -5 'parttern' inputfile //打印匹配行的前后5行

$grep -C 5 'parttern' inputfile //打印匹配行的前后5行

$grep -A 5 'parttern' inputfile //打印匹配行的后5行

$grep -B 5 'parttern' inputfile //打印匹配行的前5行



//搜索inputfile中满足parttern的内容的行号

grep -n 'parttern' inputfile

查看某文件inputfile指定行号(90)后的内容
tail -n +90 inputfile

查看文件inputfile的第190行到196行

sed -n '114,196p' inputfile