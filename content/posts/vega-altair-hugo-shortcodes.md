---
title: Додавання Vega-Lite візуалізацій в Hugo за допомогою Shortcodes
date:  2024-07-23
draft: false
tags:
  - Dataviz
  - Hugo
languages:
  - Altair
  - Vega-Lite
  - Hugo

load_vega: true
---


Код специфікації vega-lite, що треба додати: 
```json

{
  "$schema": "https://vega.github.io/schema/vega-lite/v5.json",
  "description": "A simple bar chart with embedded data.",
  "data": {
    "values": [
      {"a": "A", "b": 28}, {"a": "B", "b": 55}, {"a": "C", "b": 43},
      {"a": "D", "b": 91}, {"a": "E", "b": 81}, {"a": "F", "b": 53},
      {"a": "G", "b": 19}, {"a": "H", "b": 87}, {"a": "I", "b": 52}
    ]
  },
  "mark": "bar",
  "encoding": {
    "x": {"field": "a", "title": "aoesuthaosth", "type": "nominal", "axis": {"labelAngle": 0}},
    "y": {"field": "b", "type": "quantitative"}
  }
}


```


Замість ручного додавання скриптів та ембеддингу як в [туторіалі](https://vega.github.io/vega-lite/usage/embed.html), є можливість підійти трохи гнучкіше і завантажувати vega-lite бібліотеки лише на тих сторінках на яких вони потрібні, а також параметризувати деякі із штук за замовчуванням:


```html
<!-- Create a unique ID for the div where the Vega-Lite visualization will be rendered -->
<div class="vegaVis" id="{{ .Get "id" | default "vega-lite-vis" }}"></div>
<script>
  const spec = {{ .Inner | safeJS }};
  spec['width'] =  spec['width']? spec['width'] : {{ .Get "width" | default 700}};
  spec['height'] = spec['height']? spec['height'] : {{ .Get "height" | default 300}};
  vegaEmbed('#{{ .Get "id" | default "vega-lite-vis" }}', spec, {"actions": false}).catch(console.error);

</script>

```



Використання (прибрати пробіл до/після `< >`, які я додав щоб воно не рендерилось): 


```
{{ < vega-lite id="unique-vis-id-1" actions="true"> }}
{
  "$schema": "https://vega.github.io/schema/vega-lite/v5.json",
  "description": "A simple bar chart with embedded data.",
  "data": {
    "values": [
      {"a": "A", "b": 28}, {"a": "B", "b": 55}, {"a": "C", "b": 43},
      {"a": "D", "b": 91}, {"a": "E", "b": 81}, {"a": "F", "b": 53},
      {"a": "G", "b": 19}, {"a": "H", "b": 87}, {"a": "I", "b": 52}
    ]
  },
  "mark": "bar",
  "width": 300,
  "encoding": {
    "x": {"field": "a", "title": "aoesuthaosth", "type": "nominal", "axis": {"labelAngle": 0}},
    "y": {"field": "b", "type": "quantitative"}
  }
}
{{ < /vega-lite > }}
```

Результат: 
{{< vega-lite id="unique-vis-id-1" >}}
{
  "$schema": "https://vega.github.io/schema/vega-lite/v5.json",
  "description": "A simple bar chart with embedded data.",
  "data": {
    "values": [
      {"a": "A", "b": 28}, {"a": "B", "b": 55}, {"a": "C", "b": 43},
      {"a": "D", "b": 91}, {"a": "E", "b": 81}, {"a": "F", "b": 53},
      {"a": "G", "b": 19}, {"a": "H", "b": 87}, {"a": "I", "b": 52}
    ]
  },
  "mark": "bar",
  "width": 300,
  "encoding": {
    "x": {"field": "a", "title": "aoesuthaosth", "type": "nominal", "axis": {"labelAngle": 0}},
    "y": {"field": "b", "type": "quantitative"}
  }
}
{{< /vega-lite >}}


{{< vega-lite id="entso-e" dataUrl="https://github.com/olehomelchenko/ua-entso-e-transfers/raw/main/output.json">}}
{
    "$schema": "https://vega.github.io/schema/vega-lite/v5.json",
    "description": "Daily import and export visualization",
    "data": {
        "url": "https://github.com/olehomelchenko/ua-entso-e-transfers/raw/main/output.json"
    },
    "transform": [
        { "calculate": "0-datum.Export_from_UA", "as": "Export" },
        { "calculate": "datum.Import_to_UA", "as": "Import" }
    ],
    "width": 800,
    "layer": [
        {
            "mark": "bar",
            "encoding": {
                "x": { "field": "Date", "type": "temporal", "title": "Date", "timeUnit": "yearmonthdate" },
                "y": { "field": "Import", "type": "quantitative", "title": "MW", "aggregate": "sum" },
                "color": { "value": "blue" },
                "tooltip": [
                    { "field": "Date", "type": "temporal", "title": "Date",  "timeUnit": "yearmonthdate" },
                    { "field": "Import", "type": "quantitative", "title": "Import to UA (MW)", "aggregate": "sum" }
                ]
            }
        },
        {
            "mark": "bar",
            "encoding": {
                "x": { "field": "Date", "type": "temporal" ,  "timeUnit": "yearmonthdate"},
                "y": { "field": "Export", "type": "quantitative", "aggregate": "sum" },
                "color": { "value": "red" },
                "tooltip": [
                    { "field": "Date", "type": "temporal", "title": "Date",  "timeUnit": "yearmonthdate" },
                    { "field": "Export", "type": "quantitative", "title": "Export from UA (MW)", "aggregate": "sum" }
                ]
            }
        }
    ]
}
{{< /vega-lite >}}