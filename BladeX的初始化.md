# BladeX Boot 后端

解压 **BladeX的Maven环境及仓库.zip** 其中文件结构 \BladeX的Maven环境及仓库.zip\Maven-3.9.6

其中 apache-maven-3.9.6 为BladeX所需的环境 ，Moren 为 BladeX的Maven本地仓库

BladeX 所需要的 Java环境为1.8 （在  实用工具-Windows版本\Java开发的实用工具  路径中有 java1.8的安装程序）

Idea 插件  Lombok	Plugin（必装） MybatisX	Plugin	（idea选装）



### 现在开始部署 BladeX 吧

如果是 英文环境点击 File->Settings->Plugins  下载 Chinese   Lombok Plugin   MybatisX Plugin

1. 用Idea打开文件 bladex-boot  在 Idea中 按 CTRL+ALT+Shift+S 打开项目结构
   点击左侧的  项目设置  的  项目  把SDK设置成1.8，语言级别设置为 8 之后 看看 平台设置中 SDK 是不是 Java1.8 如果没有问题就 点击应用，确定，并关闭窗口
2. 点击设置，找到 构建执行部署 > 构建工具 > Maven
   勾选始终更新快照，更改Maven主目录 为 这里是你电脑的路径\Maven-3.9.6\apache-maven-3.9.6
   勾选右侧的重写 把用户设置文件改成    这里是你电脑的路径\apache-maven-3.9.6\conf\settings.xml
   本地仓库设置为 这里是你电脑的路径\Maven-3.9.6\Moren 之后点击确定
   找到 pom.xml 文件 同步加载所有Maven 项目
3. 找到   \src\main\java\org\springblade\Application.java 下的文件 右键启动，如果启动失败并且提示命令过长，Idea会有个蓝色的提示 点击即可


前端就在该文件目录下运行npm i 就行了有报错就问GPT

**数据库初始化**
先创建数据库   字符集规则 utf8mb4   排序规则utf8mb4_0900_ai_ci    导入在\bladex-boot\doc\sql\mysql  目录下的sql文件，生成必要的数据文件

![927b8f45c8e4bea237c2119ee81eef54.png](http://asset.localhost/D%3A%5CAAA_OFFER%5C%E6%96%87%E6%A1%A3%E9%9B%86%E5%90%88%2F%2Fassets%2Ff61f8549-712c-48f6-8fcd-67682c71f2cb.png)

如果是自己创建的业务表，需要在业务表中**必须**添加以下**8个字段**，
名称 				类型				注释
create_user       bigint        创建人
create_dept      bigint         创建部门
create_time       datetime   创建时间
update_user      bigint        修改人
update_time      datetime   修改时间
status                int              状态
is_deleted          int              是否已删除
tenant_id          varchar   长度12(长度)     租户ID(默认为0，租户为各租户 id)


自己创建的文件都在 org.springblade.modules 目录下即可

按照业务需求创建  controller   entity  mapper   service  等软件包
