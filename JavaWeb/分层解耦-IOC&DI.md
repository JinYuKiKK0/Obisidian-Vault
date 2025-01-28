> 高内聚，低耦合：在Controller层进行构建时，需要创建Services对象，Services层需要创建Dao对象，如果对象更改名字，其他层也需要变动，耦合度过高；为了进一步降低耦合度，分层解耦，
> 引入IOC与DI

- 控制反转：IOC(Inversion Of Control)。对象的创建控制权交由外部容器统一管理
	- 由容器创建，管理对象。这个容器称为IOC容器


- 依赖注入: DI(Dependency Injection)。容器为应用程序提供运行时所依赖的资源
	- EmpController程序运行时需要EmpService对象，Spring容器就为其提供并注入EmpService对象。


- bean对象：IOC容器中创建、管理的对象

#### IOC
> 在实现类上方添加注解`@Component`，代表将当前类放入容器内管理

为了更好的标识Web开发中，bean对象到底归属那一层，提供`Component`的衍生注解
- Controller  标注在控制层上
- Service   标注在业务层上
- Repository   标注在数据访问层
- Component   不属于三层架构

#### DI
> 在需要依赖处的上方添加注解`Autowired`（自动装配），代表容器将在此处自动注入依赖

- 属性注入
```Java
@RestController
public class UserController {

    //方式二: 构造器注入
    private final UserService userService;
    
    @Autowired //如果当前类中只存在一个构造函数, @Autowired可以省略
    public UserController(UserService userService) {
        this.userService = userService;
    }
    
 }   
```
隐藏类的依赖关系，会破坏类的封装性
- 构造函数注入
```Java
@RestController
public class UserController {

    //方式二: 构造器注入
    private final UserService userService;
    
    @Autowired //如果当前类中只存在一个构造函数, @Autowired可以省略
    public UserController(UserService userService) {
        this.userService = userService;
    }
    
 }   
```

##### 多个bean类注入
> @Autowired注解，默认是按照类型进行注入

若@Autowired对应多个bean，spring将会报错。
1. @Primary
2. @Autowired + @qualifier
3. @Resource