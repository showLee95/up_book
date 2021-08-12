# CDN缓存  
   * 代理层缓存

	
####  内容分发网络 CDN 简介
 > 内容分发网络（Content Delivery Network，CDN）通过将站点内容发布至遍布全国的海量加速节点，使其用户可就近获取所需内容，避免网络拥堵、地域、运营商等因素带来的访问延迟问题，有效提升下载速度、降低响应时间，提供流畅的用户体验。


* cdn解决的问题  
	> 1. 业务服务器接入互联网的带宽有限  
	> 1. 不同运营商的多分布  
	> 1. 不同地域的距离问题  
	> 1. 安全   

* cdn访问基本流程
	> 用户向浏览器提供要访问网站的域名，域名解析的请求被发往网站的DNS域名解析服务器；   
	> 由于网站的DNS域名解析服务器对此域名的解析设置了CNAME，请求被指向CDN网络中的智能DNS负载均衡系统；   
	> 智能DNS负载均衡系统对域名进行智能解析，将响应速度最快的节点IP返回给用户；   
	> 浏览器在得到速度最快节点的IP地址以后，向CDN节点发出访问请求；   
	> 由于是第一次访问，CDN节点将回到源站取用户请求的数据并发给用户；   
	> 当有其他用户再次访问同样内容时，CDN将直接将数据返回给客户，完成请求/服务过程。   


* 调度  
* 缓存 
* 安全  
* 视频支持  