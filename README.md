## Introduction

This project shows a way of how to use pyecharts library in tornado projects. See the nice project [pyecharts](https://github.com/pyecharts/pyecharts/).

## Usage

Install requirements:

```
pip install requirements.txt
```

Run server:

```
python server.py
```

And you can visit `http://localhost:9999/draw` to see the chart.

## Write yourself

First you need to write a basic tornado server, and create a template directory, and then --

### Add templates

Add this template to your templates directory.

The template comes from the [flask one](http://pyecharts.org/#/en-us/flask?id=step-2-provide-your-own-template), notice that the body part has changed to tornado way with `{% raw *vars* %}`, to protect the *HTML* grammer not being escaped.

```
<!DOCTYPE html>
<html>

<head>
    <meta charset="utf-8">
    <title>Proudly presented by ECharts</title>
    {% for jsfile_name in script_list %}
    <script src="{{ host }}/{{ jsfile_name }}.js"></script>
    {% end %}
</head>

<body>
{% raw myechart %}
</body>

</html>
```

### Use render() method point to charts

Save the code below as `demohandler.py`.

```
from tornado.web import RequestHandler
import random
from pyecharts import Scatter3D

REMOTE_HOST = "https://pyecharts.github.io/assets/js"


def scatter3d():
    data = [generate_3d_random_point() for _ in range(80)]
    range_color = [
        "#313695",
        "#4575b4",
        "#74add1",
        "#abd9e9",
        "#e0f3f8",
        "#fee090",
        "#fdae61",
        "#f46d43",
        "#d73027",
        "#a50026",
    ]
    scatter3D = Scatter3D("3D scattering plot demo", width=1200, height=600)
    scatter3D.add("", data, is_visualmap=True, visual_range_color=range_color)
    return scatter3D


def generate_3d_random_point():
    return [
        random.randint(0, 100), random.randint(0, 100), random.randint(0, 100)
    ]


class DrawChartHandler(RequestHandler):
    async def get(self):
        s3d = scatter3d()
        self.render('pyecharts.html',
                    myechart=s3d.render_embed(),
                    host=REMOTE_HOST,
                    script_list=s3d.get_js_dependencies())

```

the directory tree of pyechart-tornado-demo is:

```
├── server.py
└── templates
    └── pyecharts.html
```

### Run

Run server:

```
python server.py
```

Visit `http://localhost:9999/draw` to see the chart.
