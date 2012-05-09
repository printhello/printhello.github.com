---
layout: blog
title: 获得豆瓣列表和Github信息
---

关于Ajax的强大功能搞Web的童鞋们应该早有耳闻，jQuery更是名声在外。这里我的blog为了
动态显示豆瓣书籍列表和Github上项目信息使用了jQuery的Ajax功能，操作简单，甚爽，下面
是操作详情

##一、jQuery的Ajax模块
关于如何使用jQuery的Ajax模块，详细的教程可以参见官方的[Document](http://api.jquery.com/category/ajax/)。
我这里只使用了较为底层的jQuery.ajax()，就实现了我要的功能。

> jQuery.ajax()带有回调函数的一般使用方法

	$.ajax({
		url:your_json	
		dataType:jsonp
		success:function(data){} // 成功时的回调函数，数据存放在data对象中。	
	
	})

也可以使用jQuery.getJSON(),简单的获取json数据。关于其他的jQuery的Ajax功能暂未用到。

##二、获取豆瓣书籍信息。
豆瓣的API中给出了详细的文档。可以参见[豆瓣API参考手册](http://www.douban.com/service/apidoc/reference/),其中
用户和收藏分别对应用户信息和用户收藏的书籍，其他如电影、音乐的接口类似。

	js:
	// 获得豆瓣上最近阅读的书籍的评论摘要	
	function getNewBook(){			
		var url= "http://api.douban.com/people/gotaly/collection?apikey=your_apikey&alt=xd"; // 构建url注意alt=xd
		$.ajax({
			url:url,
			dataType:"jsonp", // 注意类型为jsonp
			success:function(data){	
				var book_summary=data.entry[0].summary.$t;
				var book_url=data.entry[0]["db:subject"].link[1]["@href"];
				var book_img_url=data.entry[0]["db:subject"].link[2]["@href"];
				var book_img_html="<a href=" + book_url+"><img src="+book_img_url+" />"							
				var book_summary_html = "<h4> 读后感：</h4> <div id='book_summary';>" + book_summary+"</div> "; 
				document.getElementById("book_img").innerHTML=book_img_html;
				document.getElementById("book").innerHTML=book_summary_html;			
				}
		})
	} // end of getNewBook	
	
	html:
	
	<div class="title">
		<h2 id="first_element" > I'm Reading: </h2>
		<div class="clear"></div>
	</div>
	<div id='douban'>
		<div id="book_img"></div>
		<div id="book"></div>
		<div class="clear"></div>
		<div id="book_imgs"> </div>
	</div>

这里根据豆瓣返回的json文件取得相应的信息，其第一个对象为author，这里并不需要，其他的书籍信息在entry里面，一次用下标索引，然后的summary记录的
是书籍评论的摘要，其保存在$t里面。
这里使用的第二个内容是每本书的url，存放在entry[0].["db:subject"].link[1]["@href"]里面，每本书的信息放在db:xxx里面，其中db:subject保存了书籍主题
相关信息，有3个相关连接，其中第二个和第三个分别是书籍的url和数据图片的url，放在@href中。因此上面通过link[1]["@href"]和link[2]["@href"]分别得到
书籍和其图片的连接，加入到html代码中。

##获取Github上Repos相关信息

Github提供了强大的pages功能除外，又提供了API供我们使用，目前的版本是[API_V3](http://developer.github.com/v3/),可以方便的接受json信息。
Repos目录下是和项目相关的信息。这里我使用了项目的最新提交信息。

	js:

	function getCommits(repo,div_n){			
	// repo为项目名，div_n为html代码中的相关id值,这里可以是repos_1/repos_2
	var url= "https://api.github.com/repos/gota-git/"+repo+"/commits"; //构建ajax url
	$.ajax({
		url:url,
		dataType:"jsonp", // 使用jsonp格式
		success:function(data){
			var commits_html; // 存放评论html代码
			commits_html=data.data[0].commit.author.date; 
			commits_html+="("+data.data[0].commit.author.name+"):"
			commits_html+=data.data[0].commit.message;					
			document.getElementById(div_n).innerHTML=commits_html;			
		}
		})
	} //end of getCommits

	html:
	
	<div class="title">
		<h2 > My Repos: </h2>
		<div class="clear"></div>
	</div>
	<div id='repos' >
		<h3>XXX:</h3>
		<div id="repos_1"></div>
		<h3>XXX:</h3>
		<div id="repos_2"></div>
	</div>

接受到的数据中data.data[0].commit.author为最后一次提交作者的信息,data为提交时间,name为提交者ID，提交的信息在data.data[0].commit.message中。其他字段
的意义可以在浏览器里直接输入请求url观看json文件可以很容易的知道。

## 小结：

我对javascript并不是很熟，对Ajax和jQuery更是没有做过什么研究，但是通过这次使用经历，可以看到，只要熟读相关文档，API手册，使用第三方Ajax接口是如此的
方便，推荐各位站长使用。
