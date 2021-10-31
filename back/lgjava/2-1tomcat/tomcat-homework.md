- 扫描server.xml加载配置
- 读取webXml，获取映射关系
- 使用自定义类加载加载Servlet，并初始化
- 封装成configuationg对象
- 初始化tomcat，开启端口监听
- 接收请求，解析请求，使用Mapper找到servlet
- MappedHost、Context、wrapper



http://localhost:8080/app1/shred

http://localhost:8080/app2/shred

http://localhost:8080/app2/shred2

/root/tools/nginx/ngx_http_consistent_hash-master

./configure --add-module=/root/tools/nginx/ngx_http_consistent_hash-master