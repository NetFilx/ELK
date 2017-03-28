# ELK
Elasticsearch+Logstash+Kibana搭建实时日志分析平台

# 搭建环境

MacOS Sierra+Java8

# 搭建过程

### logstash

1. 使用以下的命令：

```brew install logstash```

2. 安装好后使用以下命令检查是否安装成功

```
$ logstash --version  
#(output)logstash 5.2.2
```

### Elasticsearch

1. 安装命令（ps：最好使用的Java环境是1.8，不然会有奇奇怪怪的错误）

```brew install elasticsearch```

2. 安装好之后使用以下指令查看是否安装成功

```
$ elasticsearch --version
(output)Version: 5.2.2, Build: f9d9b74/2017-02-24T17:26:45.835Z, JVM: 1.8.0_121
```

3. 启动停止Elasticsearch

```
brew services start elasticsearch
brew services stop elasticsearch
```

或者在Elasticsearch的安装目录下使用(brew安装的路径一般都是/usr/local/Cellar中的)

```bin/elasticsearch```

当然，还可以将elasticsearch添加至环境变量中

4. 浏览器访问```http://localhost:9200``` 可以看到如下信息

```
{
  "name" : "8dfa-dd",
  "cluster_name" : "elasticsearch_limbo",
  "cluster_uuid" : "Np6rkkJWTHSAfOOql902Pw",
  "version" : {
    "number" : "5.2.2",
    "build_hash" : "f9d9b74",
    "build_date" : "2017-02-24T17:26:45.835Z",
    "build_snapshot" : false,
    "lucene_version" : "6.4.1"
  },
  "tagline" : "You Know, for Search"
}
```

### Kibana

1. Kibana不需要通过brew安装，直接下载压缩包后，解压
2. 在Kibana目录下使用bin/kibana,然后访问```http://localhost:5601``` 什么都不用管。

### X-Pack

1.在Elasticsearch 5.x的时代，监控和管理由X-Pack统一完成，包含：

- 安全：用户权限管理
- 告警：自动告警
- 监控：监控Elasticsearch集群的状态
- 报告：发送报告、导出数据
- 图表：可视化数据

2. 在安装X-Pack之前，需要停止Kibana和Elasticsearch

3. 然后分别在Kibana和Elasticsearch目录下输入

   ```
   bin/elasticsearch-plugin install x-pack
   bin/kibana-plugin install x-pack
   ```

4. 安装完成后，启动elasticsearch和kibana，访问kibana时发现需要登录了， 默认用户名和密码是elastic/changeme。

5. 后续可以在Management面板中进行用户和角色的配置，也可以看到新增了Reporting。

6. 在Monitoring页面中可以看到Elasticsearch和Kibana的状态，点击Indices还可以看到具体索引的状态。



### 例子

**官网上关于shakespeare(莎士比亚全集的例子)**

1. 下载官网上的 shakespeare.json 链接在[这里](https://www.elastic.co/guide/en/kibana/current/tutorial-load-dataset.html)
2. shakespeare.json中的信息格式大概是这样的：

```json
{
    "line_id": INT,
    "play_name": "String",
    "speech_number": INT,
    "line_number": "String",
    "speaker": "String",
    "text_entry": "String",
}
```

3. 然后如果安装了X-Pack插件支持，则需要输入用户名密码如下：

``` 
curl -XPUT -u elastic http://localhost:9200/shakespeare -d '
{
 "mappings" : {
  "_default_" : {
   "properties" : {
    "speaker" : {"type": "string", "index" : "not_analyzed" },
    "play_name" : {"type": "string", "index" : "not_analyzed" },
    "line_id" : { "type" : "integer" },
    "speech_number" : { "type" : "integer" }
   }
  }
 }
}
';
```

如果没有安装X-Pack的支持，则吧其中的-u elastic删了就好了

4. 上述就是在elasticsearch中建立了一个索引，之后的kibana就是用这个索引来读取展示elasticsearch中的信息。
5. 接下来就是开启kibana，kibana目录下命令行输入```bin/kibana``` ,然后在management中（x-pack安装之后setting就变成了这个了），配置index，具体可以看官网上的[说明](https://www.elastic.co/guide/en/kibana/current/tutorial-define-index.html)，或者是这篇[博客](http://blog.csdn.net/ywheel1989/article/details/60519151)
6. 至此，都已经配置结束了，只要去Discover中去检索就好啦！


**使用Logstash实时写入数据**



​    