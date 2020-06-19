# 提交说明

* 主干分支：master  -->管理源文档
* 分支：gh-pages   -->管理HTML网页

## 一、主干分支修改：
######
* 1、docs下增加MD文件
* 2、build html：mkdocs build
* 3、提交master 分支

```
	git checkout master
	git add .
	git commit -m "...."
	git push origin master
```


## 二、gh-pages干分支修改：
######
* 1、经过`mkdocs build`之后，需要将得到的html提交
* 2、切换gh-pages分支，提交等

```
	git checkout gh-pages
	echo "jackeyt.cn" > CNAME
	git add .
	git commit -m "...."
	git push origin master
```