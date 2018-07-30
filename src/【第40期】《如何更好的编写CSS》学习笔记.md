如何在项目中管理 CSS 代码？
原文链接：[How to get better at writing CSS](https://medium.freecodecamp.org/how-to-get-better-at-writing-css-a1732c32a72f)

> 总觉得中文翻译过来不太准确，所以其中重点部分做摘抄记录。

TL;DR

文章介绍两个方面的问题：
- 如何写出 **可维护性** 的 CSS 代码，
- 以及如何组织 CSS 代码

## CSS 预处理器 —— SCSS
- Variables
- Nesting
- Partials and imports
- 其他详见：https://sass-lang.com/guide（SCSS 文档地址，中文文档：https://www.sasscss.com/）

## 组织 CSS 代码 —— BEM 规范
中文资料：[⒛ [规范] CSS BEM 书写规范](https://github.com/Tencent/tmt-workflow/wiki/%E2%92%9B-%5B%E8%A7%84%E8%8C%83%5D--CSS-BEM-%E4%B9%A6%E5%86%99%E8%A7%84%E8%8C%83)

BEM is a naming convention and stands for **Block Element Modifier**.

- Blocks 命名：`.<blockName>`
- Elements 命名：`.<blockName>__<elementName>`
- Modifiers 命名：`.<blockName>[__<elementName>]--<ModifiersName>`

## 组织 CSS 文件 —— 7-1 模式
遵循两条规则：
- Write all your partials in 7 different folders.
- Import them all in one `main.scss` file located at the root level. And that’s it.

7 个目录分别是：
- base
> in here, put all your boilerplate code. By boilerplate, I mean all CSS code you’re gonna write each time you’ll start a new project. For example: typography rules, animations, utilities (by utilities, I mean classes like margin-right-large , text-center , …) and so on.

- components
> The name is explicit here. This folder contains all the components used to build your pages like your buttons, forms, swipers, popups, and so on.

- layout
> used to layout the different parts of your page, that is to say, your header, footer, navigation, section, your own grid, and more.

- pages
> You may sometimes have a page that has its own specific style, that stands out from what you do usually. Then put your style in the pages folder.

- themes
> If you have different themes for your app (dark mode, admin, and so on) put them in this folder.

- abstracts
> Put all your functions here, along with variables and mixins. In short, all your helpers.

- vendors
> what would be an app or a project without external libraries? Put in the vendors folder all files that don’t depend on you. You may want to add your Font Awesome file, Bootstrap, and stuff like that in here.

主文件：This is where you’ll import all your partials.代码示例如下：
```css

@import abstracts/variables;
@import abstracts/functions;
@import base/reset;
@import base/typography;
@import base/utilities;
@import components/button;
@import components/form;
@import components/user-navigation;
@import layout/header;
@import layout/footer;
...

```

精简版：
```
sass/
  _animations.scss
  _base.scss
  _buttons.scss
  _header.scss
  ...
  _variables.scss
  main.scss
```

## 编译 SCSS
- [node-sass](https://github.com/sass/node-sass#command-line-interface)

## 其他
### 热更新
- npm install -g live-server
- npm install npm-run-all --save-dev
- package.json
```javascript
{
  ...
  "scripts": {
    "start": "npm-run-all --parallel liveserver watch",
    "liveserver": "live-server",
    "watch": "node-sass sass/main.scss css/style.css -w",
  },
  ...
}
```

### 添加 autoprefixer
- npm install autoprefixer postcss-cli --save-dev
```javascript
{
  ...
  "scripts": {
    "start": "npm-run-all --parallel liveserver watch",
    "liveserver": "live-server",
    "watch": "node-sass sass/main.scss css/style.css -w",
    "compile": "node-sass sass/main.scss css/style.css",
    "prefix": "postcss css/style.css --use autoprefixer -o css/style.css",
    "compress": "node-sass css/style.css css/style.css --output-style compressed",
    "build": "npm-run-all compile prefix compress"
  ...
}
```

作者建的DEMO：https://github.com/thomlom/scss-boilerplate

\-\-EOF\-\-