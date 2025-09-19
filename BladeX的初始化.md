[BladeX的初始化.md](https://github.com/user-attachments/files/22421327/BladeX.md)[BladeX的初始化.md](https://github.com/user-attachments/files/22421329/BladeX.md)
# BladeX Boot 后端

解压 **BladeX的Maven环境及仓库.zip** 其中文件结构 \\BladeX的Maven环境及仓库.zip\\Maven-3.9.6

其中 apache-maven-3.9.6 为BladeX所需的环境 ，Moren 为 BladeX的Maven本地仓库

BladeX 所需要的 Java环境为1.8 （在  **实用工具-Windows版本\\Java开发的实用工具**  路径中有 java1.8的安装程序）

Idea 插件  Lombok Plugin（必装） MybatisX Plugin（idea选装）

## *现在开始部署 BladeX 吧*

如果是 英文环境点击 File->Settings->Plugins  下载 **Chinese   Lombok Plugin   MybatisX Plugin**

1. 用Idea打开文件 bladex-boot  在 Idea中 按 CTRL+ALT+Shift+S 打开项目结构
   点击左侧的  项目设置  的  项目  把SDK设置成1.8，语言级别设置为 8 之后 看看 平台设置中 SDK 是不是 Java1.8 如果没有问题就 点击应用，确定，并关闭窗口
2. 点击设置，找到 构建执行部署 > 构建工具 > Maven
   勾选始终更新快照，更改Maven主目录 为 这里是你电脑的路径\\Maven-3.9.6\\apache-maven-3.9.6
   勾选右侧的重写 把用户设置文件改成    这里是你电脑的路径\\apache-maven-3.9.6\\conf\\settings.xml
   本地仓库设置为 这里是你电脑的路径\\Maven-3.9.6\\Moren 之后点击确定
   找到 pom.xml 文件 同步加载所有Maven 项目
3. **更改 src/main/resources/application-dev.yml** 这里的 application-dev.yml 把  datasource: url 中的 数据库名改成自己的， 之后启动 Redis 运行 redis-server.exe
4. 找到  \\src\\main\\java\\org\\springblade\\Application.java 下的文件 右键启动，如果启动失败并且Idea会有个**蓝色的提示 JAR清单  点击即可**，启动的时候 Idea 右下角会有 **警告启动 Lomok注解，必须启用**

5\.后端项目虽然启动成功，但是还不能用,需要配置 resources\\application.yml 中的 sign-key，crypto-key，aes-key，des-key

其中 sign-key 在 org.springblade.test.SignKeyGenerator 路径下，运行，在结果里面随便挑一个并给其赋值
其余三个在 org.springblade.test.CryptoKeyGenerator 路径下，运行，在结果里面随便挑一个并给其赋值
启动默认用的dev环境，如果需要更改，点击Idea上面的 运行\\调试配置 -> 右侧三个点更多设置 -> 配置里面的 编辑 -> 在有效配置文件中输入 test，prod, 更改环境 不填默认是 dev

## **数据库初始化**

先创建数据库   **字符集规则：utf8mb4**  **排序规则：utf8mb4\_0900\_ai\_ci**  导入在\\bladex-boot\\doc\\sql\\mysql  目录下的sql文件，生成必要的数据文件

因为BladeX 的原始表挺多的，所以在创建自己的业务表的时候可以**加上前缀以方便区分**

如果是自己创建的业务表，需要在业务表中**必须**添加以下**8个字段**，
名称 				类型				注释
create\_user     		  bigint       			创建人
create\_dept      		bigint         		      创建部门
create\_time       	       datetime   			创建时间
update\_user      	       bigint        			修改人
update\_time                     datetime   			修改时间
status                		int              		       状态
is\_deleted          	       int              			是否已删除
tenant\_id          		varchar   长度12(长度)     租户ID(默认为0，租户为各租户 id)

## 后端代码生成(简化开发)

对于BladeX框架**所有业务都写在 src/main/java/org/springblade/modules** 下面。包名一律小写，类用大驼峰命名（HuangHuai.java），方法名、变量名一律小驼峰( huangHuai )
	常量所有字母均大写   都放在  src/main/java/org/springblade/common/constant 目录下

```java
//这里的常量为 url 路径，也就是 @RequestMapping 中的路径，
public interface AppConstants extends AppConstant {
	/**
	 * 公司简称
	 */
	String APPLICATION_PROJECT_URI_PREFIX = "xxxx";
	/**
	 * 地址管理 - 接口前缀
	 */
	String APPLICATION_ADRESSES_BOOKS_PREFIX = APPLICATION_PROJECT_URI_PREFIX + "/adresses-book";
}
```

入职之后的开发都是有**固定风格的**，最简单的就是看人家怎么写的我们就怎么写，**规范是很重要**，这里我们遵循BladeX框架的风格，个人觉得BladeX的代码生成已经特别强大了
他会生成以下**文件夹及其内容  controller,entity,dto,vo,wapper,mapper,service,excel**

**controller** 文件夹中：会直接**生成** CURD (增删改查) 、分页、自定义分页、数据导出**以上功能的代码**，并切每个请求都包含了Swagger 注解，其作用可以直接生成 接口文档，**稍后讲解怎么配置Swagger注解生成接口文档**
	**entity** 文件夹：会生成我们所选择的业务数据表中的字段，并标好 Lomok，MybatisPlus 的注解
	**dto** 文件夹：会继承自我们的 Entity，开发中用 dto 在 ServiceImpl 中操作数据
	**vo** 文件夹：会继承自我们的 Entity，开发中用 vo 类返会给前端
	**wapper** 文件夹：用于进行类型之间的转换，其中已经有的方法 entity 转为 vo
	**excel** 文件夹：用于生成excel时所需要的字段 
	**service** 文件夹：其中有 自定义分页、导出数据的接口 和 impl 实现类文件夹
	**mapper** 文件夹：其中有 自定义分页、导出数据的 接口，也有他们对应 xml 文件，想要实现自定义的逻辑就直接改自定义分页对应的mapper.xml 的 sql 语句就可以了

等大家入职之后，基本上都会有代码生成的工具以提高代码开发效率

###### 	BladeX 代码生成的操作：

##### 	1.首先配置数据源管理：

​	其中里面有默认的数据源，我们可以进行更改也可以进行新建
​		**以Mysql为例**：
​				数据类型为: jdbc

​			**名称：随便填（下面要用哦！）**

​			驱动类型：选择Mysql的驱动类型

​			用户名，密码就是你数据库的用户名和密码
​				连接地址：如下，记得更改数据库的名字

```sql
jdbc:mysql://localhost:3306/具体数据库的名称?useSSL=false&useUnicode=true&characterEncoding=utf-8&zeroDateTimeBehavior=convertToNull&transformedBitIsBoolean=true&serverTimezone=GMT%2B8&nullCatalogMeansCurrent=true&allowPublicKeyRetrieval=true
```

​			备注可选

##### 	2.数据模型设计：

​	点击新建，数据源：选择我们刚建好的数据源的名字
​				    物理表名：选择你要生成的业务表
​				    之后什么都不用改，点击保存，之后会弹出是否进行模型配置点击 确定

##### 	3.模型配置：

​	如果点了取消也没事，数据模型设计会新出来一条数据，点击这个数据的最右边 “模型配置”
​		到了模型配置里面，就看实体类型是否有空的，有空的给他填上，**其他的都不要改**，之后点击右下角的提交

##### 	4.代码生成：

​	点击**新建**，选择刚建好的数据模型，其他的都不改，之后点击上面的 **模板配置** 只改作者名字，之后点击上面的 **生成配置** 只填 **后端生成路径:    前端生成路径:**  这个**路径不要填在项目里**，找到一个你找到的地方，之后点击确定。**勾选你刚配好的那条数据，点击上面的代码生成**，前后端代码就生成在你填的那个路径里面了，前端代码我们后端不用直接删了，为什么不填在项目里因为他的结构是
\src\main\java\org\springblade\modules  其中有上面说的生成的文件夹，用那个文件就把那个复制到你的项目中，如果都用就把整个目录都丢进去，**之后可能会报错，原因就是 import 导包的问题**，把加进来的每个文件的报错 import 删除，Idea会自动导包(设置里面要开启 **动态导包** 才行)  

### 接口文档及其配置

前后端启动成功之后会发现，接口文档打不来，我们先来到
**前端**在 **vue.config.js 中**的 devServer: port: proxy:  中添加转发地址，如下：

```js
devServer: {
    port: 映射端口（浏览器上地址栏的）,
    proxy: {
      '/api': {
        //本地服务接口地址
        target: 'http://localhost:后端的端口',
        changeOrigin: true,
        //远程演示服务地址,可用于直接启动项目
        //target: 'https://saber.bladex.cn/api',
        ws: true,
        pathRewrite: {
          '^/api': '/'
        },
        '/doc.html': {
         //配置接口文档的地址转发，时候就浏览↓这个地址
          target: 'http://localhost:后端的端口',
          changeOrigin: true
        }
      }
    }
  }
```

**后端**需要填写 Swagger 扫描的包，自动生成接口文档，先找到
src/main/java/org/springblade/common/config/SwaggerConfiguration.java  这个 **SwaggerConfiguration.java**类
这里直接把代码贴出来

```java
@Configuration(proxyBeanMethods = false)
@EnableSwagger
@AllArgsConstructor
public class SwaggerConfiguration {

//.......// 上面的代码都是一样的哈，为了节省篇幅就不展示了主要的在下面

    //这个Bean是自带的
	@Bean
	public Docket flowDocket() {
		return docket("工作流模块", Collections.singletonList(AppConstant.BASE_PACKAGES + ".flow"));
	}
//这个是自己写的
	@Bean        //   ↓ 这个起一个类名要求 Docket前面都是小写字母
	public Docket adreessbookDocket() {
		return docket("地址薄模块",//←这个参数要改(2)
			Arrays.asList(AppConstant.BASE_PACKAGES + ".modules.adressbook"));
     //这个参数改成这个↑类型(3)      ↑这里不动                ↑这个改成在modules下面的包名(4)
//如果再写了接口就直接复制自己写的，改以下 (1),(2) 就好了，(4)最前面的小数点→ . ←别忘了  

//.......// 下面的代码都是一样的哈，为了节省篇幅就不展示了主要的在上面
	}
```

# Saber前端部署

需要node 18 左右的版本，文件目录下的终端运行 npm i 如果有报错就去问 ChatGpt，依赖下载好之后运行   npm run serve,或者是 找到package.json 中的scripts里面的 serve 并点击 ，如果一直下载不好就换node 版本，把项目中 node\_modules 删除 再从新下载
要在本地前后端都部署，需要启动后端，找到/resources/application.yml 中 server: port:  这个端口号（比如我这里为server: port: 8080），前端需要在 **vue.config.js 中**从下往上找，找到 target: 'http://localhost',在这个后面补上端口号  (因为我的端口为8080，所以 target: 'http://localhost:8080')  
devServer下面的port: 1888, 这个端口为前端的页面代理，在浏览器中访问就是http://localhost:1888/

```js
//文件就是根目录下面的 vue.config.js
devServer: {
    port: 映射端口（浏览器上地址栏的）,
    proxy: {
      '/api': {
        //本地服务接口地址
        target: 'http://localhost:后端的端口',
        changeOrigin: true,
        //远程演示服务地址,可用于直接启动项目
        //target: 'https://saber.bladex.cn/api',
        ws: true,
        pathRewrite: {
          '^/api': '/'
        },
        '/doc.html': {
            //配置接口文档的地址转发
          target: 'http://localhost:后端的端口',
          changeOrigin: true
        }
      }
    }
  }
```

