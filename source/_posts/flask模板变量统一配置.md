---
title: flask模板变量统一配置
date: 2025-04-10 11:03:16
tags: flask
categories: flask
---

在函数内部，使用 @app.context_processor 装饰器定义了一个名为 make_template_context 的函数。这个装饰器的作用是注册一个上下文处理器，使其返回的变量在所有模板中全局可用。
参考代码如下：

```python
def register_template_context(app):
    @app.context_processor
    def make_template_context():
        admin = Admin.query.first()
        categories = Category.query.order_by(Category.name).all()
        links = Link.query.order_by(Link.name).all()
        if current_user.is_authenticated:
            unread_comments = Comment.query.filter_by(reviewed=False).count()
        else:
            unread_comments = None
        return dict(
            admin=admin, categories=categories,
            links=links, unread_comments=unread_comments)
```
