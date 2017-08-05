# 个人博客myblog
个人blog--学习笔记
[https://mronion0603.github.io/myblog/](https://mronion0603.github.io/myblog/)

## Jekyll-Jacman主题

Jekyll-Jacman 是为 [Jekyll](http://jekyllrb.com) 设计的一款清新且具有响应式的主题，拥有更丰富的特性并支持了很多的国内服务。Jacman 始于 [Hexo Jacman](https://github.com/wuchong/jacman) 移植而来。

## 本地搭建

确保已安装`Ruby 2.1.0` 或更高版本：

```sh
ruby --version
```

安装`Bundler`：

```sh
gem install bundler
```

下载 Jacman 主题：

```sh
git clone https://github.com/mronion0603/myblog.git 
cd myblog
```

安装依赖：

```sh
bundle install
```

运行 Jekyll：

```sh
bundle exec jekyll server
```

图片插入方式：  
```sh
![](/projectname/image path/xxx.jpg)  
```
例如:
```sh
![](/myblog/assets/img/jekyll1.jpg)
```

更多细节可以参考：[Setting up your GitHub Pages site locally with Jekyll](https://help.github.com/articles/setting-up-your-github-pages-site-locally-with-jekyll/)