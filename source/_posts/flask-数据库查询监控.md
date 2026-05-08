---
title: flask 数据库查询监控
date: 2025-04-10 11:06:28
tags: flask
categories: flask
---

In debug mode Flask-SQLAlchemy will log all the SQL queries sent
to the database.  This information is available until the end of request
which makes it possible to easily ensure that the SQL generated is the
one expected on errors or in unittesting.  If you don't want to enable
the DEBUG mode for your unittests you can also enable the query
recording by setting the ``'SQLALCHEMY_RECORD_QUERIES'`` config variable
to `True`.  This is automatically enabled if Flask is in testing mode.

The value returned will be a list of named tuples with the following
attributes:

example:

```python
def register_request_handlers(app):
    @app.after_request
    def query_profiler(response):
        for q in get_debug_queries():
            if q.duration >= app.config['BLUELOG_SLOW_QUERY_THRESHOLD']:
                app.logger.warning(
                    'Slow query: Duration: %fs\n Context: %s\nQuery: %s\n '
                    % (q.duration, q.context, q.statement)
                )
        return response
```
