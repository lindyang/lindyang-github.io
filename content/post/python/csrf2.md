---
title: "Csrf2"
date: 2023-07-28T15:55:07+08:00
tags: [python]
---


##### python
```
# coding: utf-8
from __future__ import unicode_literals

import json

from flask import Flask, request, Response


app = Flask(__name__)


@app.route('/login', methods=['get', 'post'])
def login():
    """
    args: get
    form: post
    values: args + form
    get_json(): json
    """
    session_id = request.cookies.get("session_id")
    if session_id == "login":
        return Response(json.dumps({"msg": u"已登录(logined)"}), status=200, mimetype='application/json')

    content_type = request.headers.get('content_type')
    print("Content-Type: {}".format(content_type))
    if not content_type or content_type == "application/json":
        params = request.get_json(force=True)
    else:
        params = request.values
    name = params.get('name')
    password = params.get('password')

    if name and password:
        print("name={}, password={}".format(name, password))
        rsp = Response(json.dumps({"msg": u"登录成功(success)"}), status=200, mimetype='application/json')
        rsp.set_cookie('session_id', 'login')
        return rsp
    return Response(json.dumps({"msg": u"登录失败(fail)"}), status=400, mimetype='application/json')


if __name__ == "__main__":
    app.run(port=5000, debug=True)

```

##### html
```
<!DOCTYPE html>
<html>
<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width">
  <title>JS Bin</title>
</head>
<body>
  <iframe src="http://127.0.0.1:5001/login" style="display: none;">
  </iframe>
  <form method="POST" action="/login" name="login">
    <input placeholder="name" type="text" name="name" />
    <br />
    <input placeholder="password" type="password" name="password" />
    <br />
    <input type="submit" />
  </form>
</body>
</html>
```

##### nginx
```
server {
    listen 5001;
    root /opt;
    index index.html;

    location @flsk {
        proxy_pass http://127.0.0.1:5000;
    }

    location / {
        try_files $uri $uri/  @flsk;
    }
}

server {
    listen 5002;
    root /opt;
    index index.html;
}
```

