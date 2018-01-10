### stimulus 与 rails 5.1 整合 demo

**本项目参考 stimulus 的官方样例,演示了怎样把 stimulus 整合到 rails 5.1 中。因为 stimulus 官方样例大量采用 ES2016 语法，所以本项目以 webpack 形式接入到 rails ，你也可以用其他形式接入，详见最后一个参数链接**

**说明**

本项目建立时， stimulus 还处在 0.9.0 版本，正式版可能 API 变化导致 运行失败，请留意这一点。

**搭建步骤**

* step 1 新建带 webpack 的 rails 项目

    - 首先确保已安装 rails 5.1 版本。运行 `rails new rails_with_stimulus --webpack` 创建一个用 webpack 打包前端资源的新项目

    - 运行 `bundle exec rails webpacker:install` 生成相关框架文件

    - 在 `app/views/layouts/application.html.erb` 文件的 `head` 里加上这一行： `<%= javascript_pack_tag 'application' %>`

* step 2 安装 stimulus 及搭建初始环境

    - 尽量安装最新版本的 nodejs 。运行 `npm install stimulus -S` 安装 stimulus 。

    - 在 `app/javascript/packs/application.js` 文件里，加上：

    ```javascript
        import { Application } from "stimulus"
        import { autoload } from "stimulus/webpack-helpers" // 自动编译加载源码

        const application = Application.start()
        const controllers = require.context("../src", true, /\.js$/)
        autoload(controllers, application)
    ```

    - 这样就可以在目录 `app/javascript/src` 下面编写 javascript 代码了

* step 3 写一个 demo

    - 运行 `rails g controller hello` 生成 controller 和 view

    - 往 `app/controllers/hello_controller.rb` 添加一个方法：

    ```ruby
        def index
        end
    ```

    - 添加路由： `get 'hello/index', to: 'hello#index'`

    - 往 `app/views/hello/index.html.erb` 添加代码:

    ```html
        <div data-controller="hello">
            <input data-target="hello.name" type="text">
            <button data-action="click->hello#greet">Greet</button>
        </div>
    ```

    - 往 `app/javascript/src` 新建文件 `hello_controller.js` ，写入代码：

    ```javascript
        import { Controller } from "stimulus"
        export default class extends Controller {
            greet() {
                console.log(`Hello, ${this.name}!!`)
            }

            get name() {
                return this.inputElement.value
            }

            get inputElement() {
                return this.targets.find("name")
            }
        }
    ```

* step 4 运行

    - 运行 `rails s`

    - 在浏览器输入 `localhost:3000/hello/index` ，查看 demo


**参考文档**

- [rails/webpacker](https://github.com/rails/webpacker)
- [stimulus](https://github.com/stimulusjs/stimulus)
- [stimulus INSTALLING/using-webpack](https://github.com/stimulusjs/stimulus/blob/master/INSTALLING.md#using-webpack)