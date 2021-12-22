1.去官网下载项目压缩文件

2.解压文件，并建立xxl_job数据库执行该脚本
“调度数据库初始化SQL脚本” 位置为:
/xxl-job/doc/db/tables_xxl_job.sql

3.将项目导入IDEA,并且修改xxl-job-admin项目的mysql连接信息

4.将xxl-job-admin打包成jar文件(改名为xxl-job.jar)
https://blog.csdn.net/luoww1/article/details/88039311

5.上传jar文件到centos服务器

6.执行java -jar xxl-job.jar

7.登录访问
http://1.15.72.181:8080/xxl-job-admin/
admin/123456