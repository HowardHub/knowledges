Linux下软件安装规范

所有软件下载在root的~目录

所有软件解压后移动到/usr/local/softName

demo

```bash
# 下载
wget https://repo.huaweicloud.com/elasticsearch/7.6.1/elasticsearch-7.6.1-linux-x86_64.tar.gz
# 解压
tar -zxvf elasticsearch-7.6.1-linux-x86_64.tar.gz
# 移动并改名为es
mv elasticsearch-7.6.1 /usr/local/es/
```



