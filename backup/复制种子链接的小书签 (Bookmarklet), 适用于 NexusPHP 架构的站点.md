# 场景

设想这样一个场景，浏览器和网络由组织管理，PT 站只能浏览，种子文件被禁止下载，而 NexusPHP 架构的站点似乎缺少一个功能——使用 TXT 格式下载种子。这时想要临时给远程的 qB 服务器添加一个种子，只能通过带 passkey 的种子链接。

我曾经写过一个油猴脚本 [pt-helper](https://github.com/amorphobia/pt-helper)，可以在浏览种子的页面添加一个复制按钮（[效果](https://github.com/amorphobia/pt-helper/blob/b255ab3be81861d72bac32ea60d02fd669744773/README-zh.md#%E7%A7%8D%E5%AD%90%E7%9B%B4%E9%93%BE)）。然而浏览器由组织管理，禁止安装任何附加组件，油猴脚本自然不能使用了。

手动复制种子 ID 和个人 passkey 组成一个种子直链当然也是可以的，但有没有更方便的办法呢？

# 小书签 Bookmarklet

我的印象里，早在 2010 年代，浏览器[小书签](https://zh.wikipedia.org/wiki/%E5%B0%8F%E4%B9%A6%E7%AD%BE)就在网络上流行了，不过油猴脚本比它更强大，所以并没被更广泛地使用。在这个油猴脚本被禁用的场景，小书签正是大显身手的时机。

pt-helper 在添加直链的时候会将站点的 passkey 储存到油猴插件的本地数据库，小书签无法使用本地储存，所以每次点击都需要获取一次。除此之外，我从 [w3schools](https://www.w3schools.com/howto/howto_js_snackbar.asp) 找了一个简陋的即时提示，包含了额外的样式和 `div` 元素，油猴脚本可以在打开网页的初期就添加这些东西，小书签也是只能点击后添加。

# 代码

<details>
<summary>JavaScript 原始代码，点击展开</summary>

```javascript
(function () {
	l=window.location;
	if(l.pathname!="/details.php")return;
	ph=l.protocol+"//"+l.host;
	xhr=new XMLHttpRequest();
	xhr.open("GET",ph+"/usercp.php",false);
	xhr.send();
	if(xhr.readyState!=XMLHttpRequest.DONE||xhr.status!=200)return;
	d=document.implementation.createHTMLDocument().documentElement;
	d.innerHTML=xhr.responseText;
	re=/[\w\d]{32}/;
	a=d.querySelectorAll("td.rowfollow");
	for(t of a){
		m=re.exec(t.innerHTML);
		if(m){
			pk=m[0];
			break;
		}
	}
	p=new URLSearchParams(l.search);
	id=p.get("id");
	null!=pk&&""!=pk&&null!=id&&""!=id&&navigator.clipboard.writeText(ph+"/download.php?id="+id+"&passkey="+pk);
	st=document.createElement("style");
	st.textContent="#snackbar{visibility:hidden;min-width:250px;margin-left:-125px;background-color:#333;color:#fff;text-align:center;border-radius:2px;padding:16px;position:fixed;z-index:1;left:50%;bottom:30px}#snackbar.show{visibility:visible;-webkit-animation:.5s fadein,.5s 2.5s fadeout;animation:.5s fadein,.5s 2.5s fadeout}@-webkit-keyframes fadein{from{bottom:0;opacity:0}to{bottom:30px;opacity:1}}@keyframes fadein{from{bottom:0;opacity:0}to{bottom:30px;opacity:1}}@-webkit-keyframes fadeout{from{bottom:30px;opacity:1}to{bottom:0;opacity:0}}@keyframes fadeout{from{bottom:30px;opacity:1}to{bottom:0;opacity:0}}";
	document.head.appendChild(st);
	x=document.createElement("div");
	x.id="snackbar";
	x.innerHTML="已复制！链接含 passkey 请勿泄露！";
	document.getElementsByTagName("body")[0].appendChild(x);
	x.className="show";
	setTimeout(()=>{x.className=""},3000);
})();
```

</details>

原始代码经过[这个工具](https://chriszarate.github.io/bookmarkleter/)转换后，生成了紧凑的小书签代码：

```javascript
javascript:void%20function(){if((l=window.location,%22/details.php%22==l.pathname)%26%26(ph=l.protocol+%22//%22+l.host,xhr=new%20XMLHttpRequest,xhr.open(%22GET%22,ph+%22/usercp.php%22,!1),xhr.send(),xhr.readyState==XMLHttpRequest.DONE%26%26200==xhr.status)){d=document.implementation.createHTMLDocument().documentElement,d.innerHTML=xhr.responseText,re=/[\w\d]{32}/,a=d.querySelectorAll(%22td.rowfollow%22);for(t%20of%20a)if(m=re.exec(t.innerHTML),m){pk=m[0];break}p=new%20URLSearchParams(l.search),id=p.get(%22id%22),null!=pk%26%26%22%22!=pk%26%26null!=id%26%26%22%22!=id%26%26navigator.clipboard.writeText(ph+%22/download.php%3Fid=%22+id+%22%26passkey=%22+pk),st=document.createElement(%22style%22),st.textContent=%22%23snackbar{visibility:hidden;min-width:250px;margin-left:-125px;background-color:%23333;color:%23fff;text-align:center;border-radius:2px;padding:16px;position:fixed;z-index:1;left:50%25;bottom:30px}%23snackbar.show{visibility:visible;-webkit-animation:.5s%20fadein,.5s%202.5s%20fadeout;animation:.5s%20fadein,.5s%202.5s%20fadeout}%40-webkit-keyframes%20fadein{from{bottom:0;opacity:0}to{bottom:30px;opacity:1}}%40keyframes%20fadein{from{bottom:0;opacity:0}to{bottom:30px;opacity:1}}%40-webkit-keyframes%20fadeout{from{bottom:30px;opacity:1}to{bottom:0;opacity:0}}%40keyframes%20fadeout{from{bottom:30px;opacity:1}to{bottom:0;opacity:0}}%22,document.head.appendChild(st),x=document.createElement(%22div%22),x.id=%22snackbar%22,x.innerHTML=%22\u5DF2\u590D\u5236\uFF01\u94FE\u63A5\u542B%20passkey%20\u8BF7\u52FF\u6CC4\u9732\uFF01%22,document.getElementsByTagName(%22body%22)[0].appendChild(x),x.className=%22show%22,setTimeout(()=%3E{x.className=%22%22},3e3)}}();
```

# 限制

有的站点访问控制面板需要先输入密码，只能手动访问后才能正确获取到 passkey。