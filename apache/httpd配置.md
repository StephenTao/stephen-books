## 常用设置

### 1. Listen监听
省略ip表示监听本机所有IP;Listen可重复出现多次；
```
#
# Listen: Allows you to bind Apache to specific IP addresses and/or
# ports, instead of the default. See also the <VirtualHost>
# directive.
#
# Change this to Listen on specific IP addresses as shown below to 
# prevent Apache from glomming onto all bound IP addresses.
#
#Listen 12.34.56.78:80 # 监听该IP的80端口
Listen 80 #监听本机80端口
```

### 2. DirecotryIndex定义默认主页面

```
#
# DirectoryIndex: sets the file that Apache will serve if a directory
# is requested.
#
DirecotryIndex index.htm lindex.htm index.php
```


### 3. Directory

#### 3.1 Options所有可用特性：Indexes,Includes,FollowSymLinks,SymLinksifOwnerMatch,ExecCGI,MultiViews,None,All
* `Indexes`:索引；在无默认主页面又无欢迎页时，将所有资源以列表形式呈现给用户。危险，慎用；在选项前添加减号即为禁用。如-Indexes
* `FollowSymlinks`：允许跟踪符号链接文件；

#### 3.2 AllowOverride
支持在每个页面目录下创建.htaccess用于实现对此目录中资源访问时的访问控制功能； .htaccess文件会影响httpd的性能.

#### 3.3 Order 基于IP做访问控制机制
`Order` allow,deny  
`Allow` form 192.168.10.0/24  允许访问的IP
`Deny` form 192.168.9.0/24  禁止访问的IP

form后面能够接受的地址格式：  
IP，Network Address  
网络地址格式较为灵活：  
```
    172.16
    172.16.0.0
    172.16.0.0/16
    172.16.0.0/255.255.0.0
```

```
#
# This should be changed to whatever you set DocumentRoot to.
#
<Directory "C:/Program Files (x86)/Apache Software Foundation/Apache2.2/htdocs">
    #
    # Possible values for the Options directive are "None", "All",
    # or any combination of:
    #   Indexes Includes FollowSymLinks SymLinksifOwnerMatch ExecCGI MultiViews
    #
    # Note that "MultiViews" must be named *explicitly* --- "Options All"
    # doesn't give it to you.
    #
    # The Options directive is both complicated and important.  Please see
    # http://httpd.apache.org/docs/2.2/mod/core.html#options
    # for more information.
    #
    Options Indexes FollowSymLinks  #默认是开启的

    #
    # AllowOverride controls what directives may be placed in .htaccess files.
    # It can be "All", "None", or any combination of the keywords:
    #   Options FileInfo AuthConfig Limit
    #
    AllowOverride None

    #
    # Controls who can get stuff from this server.
    #
    Order allow,deny 
    Allow from all #允许所有IP访问该资源
    #Deny from all

</Directory>
```

## 4. Alias路径别名
实现URL路径的映射，从而所访问的资源不再依赖于站点根目录
```
Alias /URL/ "/path/to/somewhere/"
#Alias /ra C:\Users\shuang162\Desktop\stephen\work\require-angular-module
```


## 参考
 
 * [官方文档](https://httpd.apache.org/docs/2.2/)
 * [httpd基本配置详解](http://www.tuicool.com/articles/RRvym23)
 * [httpd-2.2 配置及用法完全攻略](http://www.cnblogs.com/big-tree/p/5841110.html)
