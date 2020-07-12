# topfullstack
NodeJs+VueJs全栈开发视频源码

web端 vue+bootstrap-vue+SSR
管理端 vue+element ui
app+小程序端 vue+uni-app
服务端(管理端) nest.js+mongoDB
服务端(客户端) nest.js+mongoDB


启动命令：`nest start -w admin`

# 项目开始：

* 托管到github

* 新建仓库
1. 勾选开源协议 MIT (比较宽松的)
2. .gitgnore 忽略的文件 选择 node

* 本地环境
1. 建立本地根目录
2. 输入` git clone ` + github 仓库地址 <br>
    ` git clone https://github.com/Smecta/topfullstack.git `
3. code . 打开


* 项目顺序

1. 首先服务端先制作，服务端分管理端和客户端两个子项目
2. 添加完数据后，制作web端


* 服务端开始
  nestjs.com 查看官网
1. 根目录下安装nestjs及脚手架 <br>
    ` yarn global add @nestjs/cli `
2. 根目录下创建 server 文件夹 <br>
    ` nest new server ` <br>
    >  which package manager would you ❤️ to use ? <br>选择 **yarn**
3. 进入 server 文件夹 <br>
   ` cd server ` 
4. 在 server 里面创建一个名为 admin 的子项目<br>
   ` nest g app admin `
5. apps/admin 此时 admin 为后台接口，里面包含完整的nest项目，包括模型、控制器
6. 启动项目，子项目模式和标准模式启动方式不同
   
    > 标准模式 启动方式 <br> ` npm run start:dev `

    > 子项目模式 启动方式 <br> ` nest start -w admin `

    这里我们选择**子项目模式**启动 

7. 在admin/main.ts 里面app.listen(3000) 下添加` console.log('http://localhost:3000')  ` <br> 来返回一个地址，可以点击查看
   
8. 在apps下的admin和server 属于两个模块，互不干扰，但两者有公用的模块需要额外添加，回到server文件夹下,建立公用模块<br>
   ` nest g lib db `
    > what prefix would you like to use for the library (default:@app)? <br>
    默认为@app 建议换成 @libs

    libs 和 app 独立存在，里面有 db 文件夹 表示数据库模块
9. 进入 admin 里面 src下的 app.module.ts 将 db 模块引入进来
    ``` 
    @Module({
        imports:[
            DbModule
        ],
    })

    ```
    它会自动从@libs/db引入过来，针对server也是一样做法

10. 连接数据库。8、9 已将数据库模块创建完毕，现在连接数据库，安装nest数据库模块 <br>
    ` yarn add nestjs-typegoose @typegoose/typegoose`

    前面nestjs-typegoose 是在nest中使用<br>
    后面@typegoose/typegoose 纯粹是基于TS的一个封装

11. 数据库模块安装，使用数据库模块       <br>
    以后数据库连接或者模型的定义不需要放到admin 或者 server 里，直接放在公用模块libs/db里面写<br>
    在db.module.ts 里引用
    ``` 
    import { TypegooseModule } 'nestjs-typegoose

    @Module({
        imports:[
            TypegooseModule.forRoot('mongodb://localhost/topfullstack',{
                useNewUrlParser: true,
                useUnifiedTopology: true,
                useCreateIndex: true,
                useFindAndModify: false,
            })
        ],
    })
    ```
    ` yarn add mongoose @types/mongoose `
    <br>
    前面的mongoose是连接数据库的模块
    <br>
    后面的@types/mongoose表示在TS里面针对mongoose的一些类型定义和类型提示

12. 数据库模型文件，建议定义到libs/db/src下
    新建models文件夹 里面存放所有数据库模型

    在models文件夹下 建立 用户模型 user.model.ts
    ```
    //首先引入@typegoose/typegoose这个模块
    export class User{}
    //导出一个用户类，来写用户模型
    @prop() 
    //装饰它的属性
    username：string 
    //一个用户一个属性

    ```
    全局引入模块 需要将db.module.ts 
    添加@Global()标记为全局
    ```
        const models = TypegooseModule.forFeature([
            User,
            Course,
            Episode,
        ]);

        @Global()
        @Module({
            imports: [
                TypegooseModule.forRoot('mongodb://localhost/topfullstack', {
                    useNewUrlParser: true,
                    useUnifiedTopology: true,
                    useCreateIndex: true,
                    useFindAndModify: false,
                }),
                models,
            ],
            providers: [DbService],
            exports: [DbService, models],
        })
    ```
13. 在admin子项目里创建一个模块和控制器
    <br>
    ` nest g mo -p admin users `
    <br>
    ` nest g co -p admin users `
    <br>
    在 mo和co 后面加-p 表示在admin下创建users

14. 使用CRUD模块可以使用一行代码实现增删改查的接口
    <br>
    ` yarn add nestjs-mongoose-crud `

15. 安装模块后，需要在users.controller.ts 里面把模型引用进来
    ``` js
    @Crud({
    model: User,
    })
    @Controller('users')
    export class UsersController {
        constructor(@InjectModel(User) private readonly model ) { }
    }
    //在constructor 把这个模型引入进来，使用Injectmodel 方式 注入模型，注入模式要写一个类 就是User，把它要注入给谁，要用 private 定义到自己对象级别的属性上 readonly 只读不能修改 后面的名字必须是 model 这里是CRUD规定的
    //使用nestjs-typegoose的 
    ```
    因需要写接口文档需要用到@nestjs/swagger 这个包<br>
    ` yarn add @nestjs/swagger swagger-ui-express `<br>
    前面@nestjs/swagger这个是官方的swagger包<br>
    后面swagger-ui-express 这个是第三方的包，基于express的swagger包<br>

16. 在 admin/main.ts 写好后台管理的接口文档<br>
    查看open swagger 的代码<br>
    ``` js
    import { SwaggerModule, DocumentBuilder } from '@nestjs/swagger';

    const options = new DocumentBuilder()
        .setTitle(' 全栈之巅-后台管理API')
        .setDescription('供后台管理界面调用的服务端API')
        .setVersion('1.0')
        .build();
    const document = SwaggerModule.createDocument(app, options);
    SwaggerModule.setup('api-docs', app, document);

    await app.listen(3000);
    ```
    里面有五个功能，增删改查，查有两个查，一个查所有，一个是查详细<br>
    
    ``` js
    //建议每个模块在控制器里面加标签去分组

    @Controller('users')
    @ApiTags('用户')
    ```

    ``` js
    //在libs/db/models/user.model.ts 增加描述
    //在模型上定义每一个属性的名字，example为示例值

    @ApiProperty({ description: '用户名', example: 'user1' })
    @prop()
    username: string;

    //在模型上定义增加创建时间和更新时间
    //modelOptions 可以定这个模型的其他属性，timestamps:true 及时创建时间和更新时间两个字段

    @modelOptions({
        schemaOptions: {
            timestamps: true,
        },
    })

    export class Episode {
    ```
后台服务端接口，增删改查，最简单，定义模型 定义字段 (指定模型，注入模型到model属性上面)
设计数据结构，数据库之间的关联，先从模型开始
复制用户模型 建立课程模型
遵循nest官方的一个规范
    建立模型命名上注意：模型使用单数，描述一个模型类，类指的是个单数
    建立模块和控制器习惯使用复数
只是属性定义，仅仅停留在js层面，赋值后不会保存到数据库里面
只有定义了@prop()才是数据库字段
只有定义了@ApiProperty()才是nest swagger里面展示数据的字段

如果有内嵌式模型，建议使用关系型
这里使用关系型数据库方式，用ID进行关联
课程里面有课时  课程是大类，课时是每一集的
表示数组字段的时候，不能使用@prop()字段，在typegoose里面使用@arrayProp()
类型不能是普通类型，在typegoose里面使用参考类型 Ref 需要定一个范类，因是一个数据后面加个中括号，Ref<Episode>[]
这里的定义为了类型定义，能够写代码进行提示
也要在@arrayProp()里面定义{itemsRef:}因为它数据每一个元素参考那个，给mongoose使用，itemsRef:
后面写字符串的"Episode" 在JS里面为了避免循环引用，建议使用字符串
@arrayProp({itemsRef:"Episode"})

* 后台管理端开始 
   考虑vue3.0 使用typeScript开发，直接使用typescript开发vue项目
   两种语法，一种是vue extends 一种是class Component
   基于类的Vue组件
   如果您在声明组件时更喜欢基于类的 API，则可以使用官方维护的 vue-class-component 装饰器：
``` js
        import Vue from 'vue'
        import Component from 'vue-class-component'

        // @Component 修饰符注明了此类为一个 Vue 组件
        @Component({
            // 所有的组件选项都可以放在这里
            template: '<button @click="onClick">Click!</button>'
        })
        export default class MyComponent extends Vue {
            // 初始数据可以直接声明为实例的属性
            message: string = 'Hello!'

            // 组件方法也可以直接声明为实例的方法
            onClick (): void {
            window.alert(this.message)
            }
        }
```        
   使用@Component 修饰符注明此类为一个Vue组件


1. 与server同级，创建一个admin文件夹用vue创建 <br>
   ` vue create admin `
    > 使用默认即可 default(babel, eslint)
    > 使用 yarn 安装
2. 进入admin 文件夹，添加element ui
   ` vue add element `
   > still proceed？(y/N) 输入y 继续 YES
   > Fully import 使用完全引入
   > SCSS?(y/N) 输入回车 也就是no
   > zh-CN 使用中文
3. 添加 router 路由
   ` vue add router `
   > still proceed？(y/N) 输入y 继续 YES
   >Use history?(Y/n) 输入n 暂时不需要使用history模式 NO
4. 添加完 2和3 后再转换成typescript项目
   ` vue add typescript `
   > still proceed？(y/N) 输入y 继续 YES
   > Use class-style?(Y/n) 回车 选择默认使用类风格的 YES
   > Use Babel alongside TypeScript(Y/n) 回车 选择默认 YES
   > Conver all .js files to .ts?(Y/n) 回车 选择默认转换js为ts YES
   > Allow .js files to be compiled?(y/N) 回车 选择默认 NO
5. 找到admin/src/main.ts
   删除引入文件的` import './plugins/element.js' `的 .js
6. 使用 yarn serve 启动

后台界面，需要一个主要的布局文件
在App.vue里 调整如下
``` ts
        <template>
            <div id="app">
                <router-view></router-view>
            </div>
        </template>

        <script lang="ts">
        import { Component, Vue } from 'vue-property-decorator';


        @Component({

        })
        export default class App extends Vue {}
        </script>

        <style>
            body{
                margin:0;
            } 
            .avue-upload__avatar {
                height: auto !important; 
            }
        </style>

```


环境变量
    ` yarn add @nestjs/config ` <br>
    ` nest g lib common ` 创建一个通用公共模块 <br>
   >  ? waht prefix would you like to use for the library (default: @app)? 默认回车
    common.module.ts 下加载config包
   ```
   import { configMoudle } from '@nestjs/config'

   @Module({
       imports:[
           ConfigMoule.forRoot({
               isGlobal: true 
           }),
           DbModule,
       ],
   })
   ```

   在server 根目录下新增.env 并且在 .gitignore 添加 .env 忽略文件
   复制 .env 为 .env.example 作为范例文件供其他人查看

   为了避免同时加载，无法获取数据，这里
   用到forRootAsync()异步加载方法，里面使用异步方法useFactory()
   ```
    @Global()
    @Module({
        imports: [
            TypegooseModule.forRootAsync({
                useFactory() {
                    return {
                        uri: process.env.DB,
                        useNewUrlParser: true,
                        useUnifiedTopology: true,
                        useCreateIndex: true,
                        useFindAndModify: false,
                    };
                },
            }),
        ]
    )}    
   ```
   在app.module下引入commonModule

   解决esline的报错 any 问题
   在admin下 teconfig.json 
   新增一个 ` "noImplicitAny": false, `关闭any提示问题（另一种解决方法加:any）
   就不会提示报错了

   # 前端界面
    前端框架用vue Ui -vuetifyjs/vuetify
    Nuxt.js 提供了先渲染后展示的应用框架
    提供了优化SEO的解决方案 SSR
    
    安装NuxtJs

    在根目录下输入 
   ` yarn create nuxt-app web `  web为应用名
   > ? Project name (web) 输入项目名称 topfullstack

   > ? Project description  项目描述 直接回车

   > ... 默认回车

   > ? Choose the package manager ? 选择yarn

   > ？Choose UI framework(Use arrow keys) 选择UI框架 选择Vuetify.js

   > ? Choose custom server framework(Use arrow keys) 选择服务端框架 暂时选择None不需要，因没有nest

   > ? Choose Nuxt.js modules 选择Axios、PWA、DotEnv 全选

   > ? Choose linting tools 选择Eslint、Prettier 

   > ? Choose test framework 选择测试框架，暂时不需要None

   > ? Choose rendering mode 选择渲染模式 选择SSR
   
   > ? Choose development tools 选择开发工具，目前只有一个vscode，空格勾上


## 需要用到东西：
