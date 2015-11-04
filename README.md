# Oligrapher 2
Oligrapher is a JavaScript app for visualizing network graphs. Oligrapher accepts graph data of a specific format and allows a user to design a nice-looking SVG rendering of the graph.

Oligrapher 1 was originally developed by [LittleSis](http://littlesis.org) before it was separated into a standalone library. LittleSis has a large collection of [maps created with Oligrapher](http://littlesis.org/oligrapher). 

Oligrapher 2 is built with [React](http://reactjs.com) and [Redux](http://rackt.org/redux) and is easy to embed in a web page or web application.

![Oligrapher Demo Screenshot](https://cloud.githubusercontent.com/assets/981611/10861420/b209fc5a-7f54-11e5-82e9-164a5fca11c8.png)

Install
-------
This repository is a small Node.js application with a single demo page. To simple view the demo, point your browser to the /build directory. If you want to tinker with the code, install the dependencies and launch the dev server:

```
npm install
npm run dev
```

Then visit ```http://localhost:8080/demo```.


Embed
-----
Oligrapher is easy to embed in a web page. All you have to do is include the .js and .css files from the /build directory in your page header and mount it in an HTML element.

```
<html>
  <head>
    <link href="/path/to/oligrapher.css" rel="stylesheet"/>
    <script src=/path/to/oligrapher.min.js"></script>
    <style>
      #graph {
        width: 900px;
        height: 600px;
      }
    </style>
  </head>
  <body>
    <div id="graph"></div>
    <script>
      var div = document.getElementById('#graph');
      var data = getDataFromSomewhere();
      Oligrapher.run(div, data);
    </script>
  </body>
</html>
```


Data Schema
-----------
Graph data coming in (and out) of Oligrapher should conform to the following general structure.

```
{
  id: 'NkpdQPQfx',
  nodes: {
    1: { id: 1, display: { name: "Node 1" } },
    2: { id: 2, display: { name: "Node 2" } },
    3: { id: 3, display: { name: "Node 3" } }
  },
  edges: {
    1: { id: 1, node1_id: 1, node2_id: 2, display: { label: "Edge 1" } },
    2: { id: 2, node1_id: 2, node2_id: 3, display: { label: "Edge 1" } }
  },
  captions: {
    1: { id: 1, display: { text: "Caption 1" } }
  }
};
```

The ```id``` of the graph itself is optional, Oligrapher will generate it if not provided. Nodes only require an ```id``` and a ```name```; edges also require ```node1_id``` and ```node2_id```, which refer to the ```id```s of the two nodes they connect. A number of optional attributes control the layout and appearance of nodes and edges. All position coordinates are relative to the center of the display area, with the x-axis increasing righward and the y-axis increasing downward.

### Node Attributes
- ```id:``` **(required)** an integer or string uniquely identifying the node
- ```display:``` **(required)** an object with attributes related to the node's appearance
  - ```name:``` **(required)** a string, which will be displayed underneath the node (on multiple lines if necessary)
  - ```scale:``` relative size of node (```1``` is default, ```1.5``` is 50% wider, etc)
  - ```status:```  default is ```normal```, can also be ```highlighted``` or ```faded```
  - ```x:``` x-coordinate of the node's position
  - ```y:``` y-coordinate of the node's position

### Edge Attributes
- ```id:``` **(required)** an integer or string uniquely identifying the edge
- ```node1_id:``` **(required)** equal to the first node's id
- ```node2_id:``` **(required)** equal to the second node's id
- ```display:``` **(required)** an object with attributes related to the edge's appearance
  - ```label:``` **(required)** label appearing above the edge
  - ```scale:``` relative thickness of edge (```1``` is default, ```2``` is twice as thick, etc)
  - ```status:```  default is ```normal```, can also be ```highlighted``` or ```faded```
  - ```arrow:``` whether the edge has an arrow indicating a direction (default is ```false```)
  - ```dash:``` the kind of dash displayed in edge's line (default is ```null```, a solid line)
  - ```cx:``` x-coordinate of the control point for the edge's quadratic [Bezier curve](https://developer.mozilla.org/en-US/docs/Web/SVG/Tutorial/Paths#Bezier_Curves), *relative to the midpoint of the straight line between the two nodes* (if not specified, this is computed to display a slight curve)
  - ```cy:``` y-coordinate of the control point for the edge's quadratic [Bezier curve](https://developer.mozilla.org/en-US/docs/Web/SVG/Tutorial/Paths#Bezier_Curves), *relative to the midpoint of the straight line between the two nodes* (if not specified, this is computed to display a slight curve)fa

### Caption Attributes
- ```id:``` **(required)** an integer or string uniquely identifying the caption
- ```display:``` **(required)** an object with attributes related to the captions's appearance
  - ```text:``` **(required)** the caption's text content
  - ```x:``` x-coordinate of the caption's position
  - ```y:``` y-coordinate of the caption's position

API
---

### ```run(DOMElement, data = null)```
Starts Oligrapher within a given ```DOMElement``` and loads initial ```data``` if provided. Returns the top-level Redux [Provider](https://github.com/rackt/react-redux/blob/master/docs/api.md#provider-store) instance.
```
var div = document.getElementById('#graph');
var data = getDataFromSomeWhere();
Oligrapher.run(div, data);
```

### ```import(data)```
Imports graph ```data``` into Oligrapher and displays it, returning the imported graph's ```id```.
```
var id1 = Oligrapher.load(data1);
var id2 = Oligrapher.load(data2);
```

### ```export()```
Returns a snapshot of the currently displayed graph data, including display attributes. This data can be ```import```ed later to display the identical graph.
```
var data = getGraphFromDatabase();
Oligrapher.import(data);
// ... user edits graph ...
data = Oligrapher.export();   // get data snapshot
// ... user edits graph some more ...
Oligrapher.import(data);   // restore data snapshot
// ... user edits graph some more ...
data = Oligrapher.export();
saveGraphToDatabase(data);
```

### ```show(id)```
Displays the previously-imported graph with the given ```id```.
```
var id1 = Oligrapher.load(data1);
var id2 = Oligrapher.load(data2);
Oligrapher.show(id1);
```

### ```zoomIn()```
Zooms in by 20%. This can be triggered with the keyboard shortcut ```ctrl+equals```.

### ```zoomOut()```
Zooms out by 20%. This can be triggered with the keyboard shortcut ```ctrl+minus```.
```
Oligrapher.load(data);
Oligrapher.zoomIn();
Oligrapher.zoomOut();   // back to normal
```
