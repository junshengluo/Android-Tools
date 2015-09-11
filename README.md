说明文件

touch README.md //新建说明文件
git init //在当前项目目录中生成本地git管理,并建立一个隐藏.git目录
git add . //添加当前目录中的所有文件到索引
git commit -m "first commit" //提交到本地源码库，并附加提交注释
git remote add origin https://github.com/chape/test.git //添加到远程项目，别名为origin
git push -u origin master //把本地源码库push到github 别名为origin的远程项目中，确认提交