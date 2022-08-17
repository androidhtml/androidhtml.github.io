---
layout: post
title: '利用域前置技术绕过GFW解决SNI RST'
date: 2022-8-8
author: 水(⊙o⊙)啊
cover: 'https://cloud.shuia.tk/img/76751499-d19c-4c6f-9eaf-e6e9aed447ea.png'
tags: 水(⊙o⊙)啊
--- 
<br />
啊哈哈哈
<h2 id="支持的站点" style="font-weight:normal;color:#159957;font-family:&quot;">
	介绍
</h2>
<h2 id="支持的站点" style="font-weight:normal;color:#159957;font-family:&quot;">
	<span style="color:#606C71;">解决SNI RST导致维基百科、Pixiv等站点无法访问，绕过GFW的一种解决思路。注：教程来自于网上，与本人无关，思路仅供参考，请与24小时内删除！</span><span style="font-family:&quot;color:#606C71;font-size:12px;"></span><span style="font-family:&quot;color:#606C71;font-size:12px;"></span>
</h2>
<h2 id="前言">
</h2>
<h2 id="支持的站点" style="font-weight:normal;color:#159957;font-family:&quot;">
	前言
</h2>
<p>
	在一些特殊的时期，你的科学上Internet服务不好用时，但又要下一些github上的东西或想上Pixiv时，这篇文章或许对你有所帮助。
</p>
<h2 id="原理">
</h2>
<h2 id="支持的站点" style="font-weight:normal;color:#159957;font-family:&quot;">
	原理
</h2>
<p>
	HTTPS（更准确来讲是TLS）有一个扩展，使得在Client Hello阶段，浏览器会向服务器明文发送要访问网站的域名。而GFW根据这一原理对特定的SNI进行阻断，这就是俗称的SNI阻断。更多的可以去看：<a href="https://zh.wikipedia.org/wiki/%E6%9C%8D%E5%8A%A1%E5%99%A8%E5%90%8D%E7%A7%B0%E6%8C%87%E7%A4%BA">维基百科</a> 
</p>
<p>
	而域前置(Domain Fronting)技术，就是利用有的服务器不验证SNI的这一特点，给GFW和服务器一个假的SNI。因为GFW没有私钥，只能通过SNI和IP判断你要访问的网站，而假的SNI就能欺骗GFW。但服务器本身有私钥，解开后，服务器是通过HTTP请求头判断你要访问哪个网站的。（但注意，特殊的比如虚拟主机、Cloudflare免费版等能在一个IP上部署多个证书的，是一定要SNI的，否则会出错。这种唯一的绕过办法就是ESNI，如果有空的话，以后再讲）因此可以通过这种方式绕过GFW的SNI阻断。
</p>
<h2 id="一些已经完整实现的开源项目">
</h2>
<h2 id="支持的站点" style="font-weight:normal;color:#159957;font-family:&quot;">
	一些已经完整实现的开源项目
</h2>
<p>
	<a href="https://github.com/bypass-GFW-SNI/main">bypass-GFW-SNI/main</a>和他的Socks5版本<a href="https://github.com/bypass-GFW-SNI/proxy">bypass-GFW-SNI/proxy</a> 
</p>
<p>
	<a href="https://github.com/SeaHOH/GotoX">SeaHOH/GotoX</a> 
</p>
<p>
	<a href="https://github.com/mashirozx/Pixiv-Nginx">mashirozx/Pixiv-Nginx</a> 注：这个是利用Nginx反向代理时不支持SNI达到目的
</p>
<h2>
</h2>
<h2 id="支持的站点" style="font-weight:normal;color:#159957;font-family:&quot;">
	另一种教程:
</h2>
<h2>
	详情见:<a href="https://urenko.github.io/Accesser/" target="_blank">https://urenko.github.io/Accesser/</a> 
</h2>
<p>
	Github仓库地址:<a href="https://github.com/Urenko/Accesser" target="_blank">https://github.com/Urenko/Accesser</a>
</p>
<p>
	Accesser 更好用，配置最少：只要过 http://127.0.0.1:7655 的都会无脑剥掉 (或者修改) SNI
</p>
<h2 id="教程以bypass-gfw-sni--dnscrypt-proxy-为例">
</h2>
<h2 id="支持的站点" style="font-weight:normal;color:#159957;font-family:&quot;">
	教程（以Bypass-GFW-SNI + DNSCrypt-proxy 为例）
</h2>
<blockquote>
	<p>
		如果觉得教程困难的话，可以去评论区下载已打包好的配置文件与程序
	</p>
</blockquote>
<p>
	Step1.去项目的Release页面下载已编译好的文件
</p>
<p>
	<a href="https://github.com/bypass-GFW-SNI/main/releases">Bypass-GFW-SNI</a> 还有 <a href="https://github.com/DNSCrypt/dnscrypt-proxy/releases">DNSCrypt-Proxy</a> 
</p>
<p>
	注：可能最新版本与本文有一点差异，但大致上方法都是相同的。
</p>
<p>
	Step2.配置DNSCrypt
</p>
<p>
	设置 fallback_resolver ，即回落DNS为你运营商的DNS，比如 114.114.114.114:53（：后面的是端口号，一般情况下DNS都是用UDP53端口的）
</p>
<p>
	设置 netprobe_address ，即网络检测地址为国内能访问的地址，比如 114.114.114.114:53
</p>
<p>
	把[sources]里的全部删掉或在前面加 # 号，因为 raw.githubusercontent.com 这个域名已经被SNI阻断了，这段留着，打开DNSCrypt可能会很慢。
</p>
<p>
	在[static]里填上
</p>
<div class="highlight">
	<div class="chroma">
		<table class="lntable">
			<tbody>
				<tr>
					<td class="lntd">
<pre class="chroma"></pre>
					</td>
					<td class="lntd">
					</td>
				</tr>
			</tbody>
		</table>
<pre class="prettyprint lang-xml">[static.'Cloudflare-DNS-DoH']
stamp = 'sdns://AgcAAAAAAAAABzEuMC4wLjESZG5zLmNsb3VkZmxhcmUuY29tCi9kbnMtcXVlcnk'
[static.'Adguard-DNS-DoH']
stamp = 'sdns://AgMAAAAAAAAADzE3Ni4xMDMuMTMwLjEzMCD5_zfwLmMstzhwJcB-V5CKPTcbfJXYzdA5DeIx7ZQ6Eg9kbnMuYWRndWFyZC5jb20KL2Rucy1xdWVyeQ'</pre>
	</div>
</div>
<p>
	将 listen_addresses 改为&nbsp; 127.0.0.1:5353（因为53端口要给Bypass-GFW-SNI用）
</p>
<p>
	&nbsp;server_names调整为&nbsp; [‘Adguard-DNS-DoH’, ‘Cloudflare-DNS-DoH’]
保存dnscrypt-proxy.toml，双击dnscrypt-proxy.exe运行。如果黑色的窗口一直在，就说明没有问题。
</p>
<p>
	Step3.配置 Bypass-GFW-SNI
</p>
<p>
	这个配置十分简单，具体的可以去看看程序自带的帮助，如果是懒人包的话双击&nbsp; MITMCA.crt&nbsp; 先导入证书(不会的Google如何导入根证书或看<a href="https://github.com/XX-net/XX-Net/wiki/GoAgent-Import-CA">这里</a>把 CA.crt 换成 MITMCA.crt 就是了)，同目录下的 MITMCA.key 是 CA&nbsp; 的私钥（不信任我的，自己去Google一下OpenSSL创建CA），运行 StartProgram.bat ，把电脑的DNS改成首选 127.0.0.1，备选为空（不会的也请Google如何修改DNS或看<a href="https://baijiahao.baidu.com/s?id=1612906041166793439">这里</a>）。用浏览器打开 https://www.pixiv.net，Done。
</p>
<h2 id="注意事项">
</h2>
<h2 id="支持的站点" style="font-weight:normal;color:#159957;font-family:&quot;">
	注意事项
</h2>
<p>
	有的网站比如Twitter、Google这类大型网站大多为IP黑洞，上述方式是没有用的。通常被SNI阻断的网站，也有DNS污染。请配合下面的其中一个：DNSCrypt、DNS over HTTPS(DoH)、DNS Over TLS(DoT)使用。个人比较喜欢<a href="http://github.com/DNSCrypt/dnscrypt-proxy">DNSCrypt-proxy</a>和<a href="https://github.com/AdguardTeam/AdGuardHome">AdGuardHome</a>。其中Adguardhome对小白来说比较友好。
</p>
<h2 id="附录">
</h2>
<h2 id="支持的站点" style="font-weight:normal;color:#159957;font-family:&quot;">
	支持的站点
</h2>
<p style="color:#606C71;font-family:&quot;font-size:17.6px;">
	理论上支持绝大部分被DNS污染和SNI RST的站点。你可以<a href="https://github.com/URenko/Accesser/wiki/%E7%9B%AE%E5%89%8D%E6%94%AF%E6%8C%81%E7%9A%84%E7%AB%99%E7%82%B9">在这里</a>找到目前默认支持的站点，但是您可以通过配置自行增加站点。
</p>
<p style="color:#606C71;font-family:&quot;font-size:17.6px;">
	<span style="font-family:&quot;font-size:12px;font-weight:normal;"></span>
</p>
<h2 id="有问题" style="font-weight:normal;color:#159957;font-family:&quot;">
	有问题？不会搭建或其它问题
</h2>
<span>评论区留言</span><br />
<div>
	<p>
		<br />
	</p>
	<h2 id="有问题" style="font-weight:normal;color:#159957;font-family:&quot;">
		转自
	</h2>
	<p>
		<a href="https://urenko.github.io/Accesser/" target="_blank">https://urenko.github.io/Accesser/</a>
	</p>
	<p>
		<a href="https://gulut.github.io/gulut-blog/post1/2020/05/31/2020-05-31-by-pass-the-gfw-by-sni/" target="_blank">https://gulut.github.io/gulut-blog/post1/2020/05/31/2020-05-31-by-pass-the-gfw-by-sni/</a>
	</p>
	<p>
		<br />
	</p>
	<div class="highlight">
	</div>
</div>
