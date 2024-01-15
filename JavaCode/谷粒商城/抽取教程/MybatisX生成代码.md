# MyBatisX插件代码生成

idea右侧Database，链接数据库，右键

![image.png](https://cdn.nlark.com/yuque/0/2023/png/25734432/1694785590054-a353b3b1-64fd-4763-aba1-8cc1be70873e.png#averageHue=%233d4144&clientId=u48e93afc-1c10-4&from=paste&height=823&id=u1daa91f8&originHeight=741&originWidth=413&originalType=binary&ratio=0.8999999761581421&rotation=0&showTitle=false&size=42008&status=done&style=none&taskId=u4baf933a-e34b-4d70-b53c-907baf9209a&title=&width=458.8889010452933)
选项说明：
![image.png](https://cdn.nlark.com/yuque/0/2023/png/25734432/1694785521939-80b237ae-db2f-4cb2-ae8d-1a5af4b4967e.png#averageHue=%233e4144&clientId=u48e93afc-1c10-4&from=paste&height=426&id=uaba31ba1&originHeight=383&originWidth=910&originalType=binary&ratio=0.8999999761581421&rotation=0&showTitle=false&size=36767&status=done&style=none&taskId=u57ff8b8b-9a0f-4dd6-907e-3e9d7f83d45&title=&width=1011.111137896409)
![image.png](https://cdn.nlark.com/yuque/0/2023/png/25734432/1694785620907-0216855f-6b6a-4080-b4cb-05dbc2d14a71.png#averageHue=%233d4144&clientId=u48e93afc-1c10-4&from=paste&height=363&id=u5cca765d&originHeight=327&originWidth=888&originalType=binary&ratio=0.8999999761581421&rotation=0&showTitle=false&size=43826&status=done&style=none&taskId=u784db7b0-f578-4d46-b38d-7d39c86d9f6&title=&width=986.6666928044078)

# 编写第一个controller
```java
@RestController
@RequestMapping("/article")
public class ArticleController {

    @Autowired
    private ArticleService articleService;

    @GetMapping("/list")
    public List<Article> test(){
        //查询数据库的所有数据
        return articleService.list();
    }

}
```
# 测试
运行启动类，访问：[http://localhost:7777/article/list](http://localhost:7777/article/list)
![image.png](https://cdn.nlark.com/yuque/0/2023/png/25734432/1694788833404-ea7b859b-4bfe-44af-a5f2-953e613be09b.png#averageHue=%23f9f9fe&clientId=u48e93afc-1c10-4&from=paste&height=1034&id=ub02aa420&originHeight=931&originWidth=1920&originalType=binary&ratio=0.8999999761581421&rotation=0&showTitle=false&size=186144&status=done&style=none&taskId=u27ae8293-2d40-40e5-9b16-157ddc2b6b1&title=&width=2133.3333898473684)