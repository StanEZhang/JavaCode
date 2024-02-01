# 查询-递归树形结构数据获取

表：pms_category

![image-20240130160333467](商品服务-API-三级分类.assets/image-20240130160333467.png)

Controller

```java
@RestController
@RequestMapping("/category")
public class CategoryController {

    @Autowired
    CategoryService categoryService;

    /**
     * 全部分类信息，以树形结构展示
     */
    @RequestMapping("/list/tree")
    public R listWithTree(){
        return R.ok().put("data", categoryService.listWithTree());
    }
}
```

Service

```java

/**
* @author HAIBIN
* @description 针对表【pms_category(商品三级分类)】的数据库操作Service
* @createDate 2024-01-12 14:48:19
*/
public interface CategoryService extends IService<Category> {

    List<Category> listWithTree();
}
```

Impl

```java
/**
* @author HAIBIN
* @description 针对表【pms_category(商品三级分类)】的数据库操作Service实现
* @createDate 2024-01-12 14:48:19
*/
@Service
public class CategoryServiceImpl extends ServiceImpl<CategoryMapper, Category>
    implements CategoryService{

    @Autowired
    private CategoryMapper categoryMapper;

    @Override
    public List<Category> listWithTree() {
        // 查出所有菜单
        List<Category> retList = categoryMapper.selectList(null);
        // 组装菜单成树状结构
        List<Category> list = retList.stream()
                // 过滤出所有顶级菜单
                .filter(category -> category.getParentCid() == 0)
                // 查找子菜单
                .map((category) -> {
                    category.setChildren(getChildren(category, retList));
                    return category;
                })
                // 按照菜单优先级排序
                .sorted(Comparator.comparingInt(Category::getSort))
                .collect(Collectors.toList());
        return list;
    }

    /**
    递归查找子菜单
    */
    private List<Category> getChildren(Category root, List<Category> all) {
        List<Category> children = all.stream()
                .filter(category -> category.getParentCid().equals(root.getCatId()))
                .map(category -> {
                    category.setChildren(getChildren(category, all));
                    return category;
                })
                .sorted(Comparator.comparingInt(Category::getSort))
                .collect(Collectors.toList());
        return children;
    }
}
```

Category

```java
    /**
     * 	对应的子分类，表中不存在的字段
     * 	如果为空(三级菜单没有孩子)，则返回给前端时序列化时不包含此字段
     */
    @JsonInclude(value = JsonInclude.Include.NON_EMPTY)
    @TableField(exist = false)
    private List<Category> children;
```

PostMan

```
localhost:9002/category/list/tree
```

查询结果展示（部分）：

```
{
    "msg": "success",
    "code": 0,
    "data": [
        {
            "catId": 1,
            "name": "图书、音像、电子书刊",
            "parentCid": 0,
            "catLevel": 1,
            "showStatus": 1,
            "sort": 0,
            "icon": null,
            "productUnit": null,
            "productCount": 0,
            "children": [
                {
                    "catId": 22,
                    "name": "电子书刊",
                    "parentCid": 1,
                    "catLevel": 2,
                    "showStatus": 1,
                    "sort": 0,
                    "icon": null,
                    "productUnit": null,
                    "productCount": 0,
                    "children": [
                        {
                            "catId": 165,
                            "name": "电子书",
                            "parentCid": 22,
                            "catLevel": 3,
                            "showStatus": 1,
                            "sort": 0,
                            "icon": null,
                            "productUnit": null,
                            "productCount": 0
                        },
                        {
                            "catId": 166,
                            "name": "网络原创",
                            "parentCid": 22,
                            "catLevel": 3,
                            "showStatus": 1,
                            "sort": 0,
                            "icon": null,
                            "productUnit": null,
                            "productCount": 0
                        },
                        {
                            "catId": 167,
                            "name": "数字杂志",
                            "parentCid": 22,
                            "catLevel": 3,
                            "showStatus": 1,
                            "sort": 0,
                            "icon": null,
                            "productUnit": null,
                            "productCount": 0
                        },
                        {
                            "catId": 168,
                            "name": "多媒体图书",
                            "parentCid": 22,
                            "catLevel": 3,
                            "showStatus": 1,
                            "sort": 0,
                            "icon": null,
                            "productUnit": null,
                            "productCount": 0
                        }
                    ]
                }
            ]
        }
    ]
}
```

# 配置网关路由与路径重写

