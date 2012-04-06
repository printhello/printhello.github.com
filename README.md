#用来学习在github上面作blog的试验

#下面是一些在使用过程中的经验。

##_config.yml
最重要的是首当其冲的配置文件，采用[YAML](http://www.ibm.com/developerworks/cn/xml/x-cn-yamlintro/ "YAML简介")格式书写的。

	markdown: rdiscount
	pygments: true
	第1行设置使用rdiscount软件包作为Markdown的解析引擎，而非默认的Maruku。第2行开启pygments支持。对于中文用户强烈建议通过配置文件_config.yml重设 markdown 解析引擎，默认的 Maruku 对中文支持不好，而使用 rdiscount 或 kramdown 均可。关于该配置文件的更多参数详见Jekyll项目维基 

##index.html
然后需要一个非同一般的index.html

##_layout 目录
_layout目录下放置的是模板。
一般会有
* default.html
* post.html

