---
layout: post
title: Leaflet, R, Markdown, Jekyll and GitHub
subtitle: Make it work in 6 steps - a short tutorial
tags: [R,leaflet,Jekyll, html, maps]
linktormd: true
leafletmap: true
always_allow_html: yes
output: github_document
---



Recently I have been struggling when trying to embed a [leaflet](https://rstudio.github.io/leaflet) map created with **RStudio** on my blog, hosted in GitHub via [Jekyll](https://jekyllrb.com) (**Spoiler**: [I succeeded <i class="fa fa-thumbs-up"></i>](https://dieghernan.github.io/2019-133-Where-in-the-world)). In my case, I use the [**Beautiful Jekyll**](https://deanattali.com/beautiful-jekyll/getstarted/) implementation created by [daattali](https://github.com/daattali).

**Index**

1. [What to include](#step1)

2. [Where to include](#step2)

3. [Creating the leaflet map](#step3)

4. [Set up the YAML front matter](#step4)

5. [Modifying the `.md`file](#step5)

6. [Publish your post](#step6)

**[Gallery: Size of a leaflet map](#extra)**

Ready? Let's go!
 
### The GitHub/Jekyll part  
 
The first step is to install the requested libraries in your GitHub page. As Jekyll basically transforms `markdown` into `html`, this step is a matter of **what to include** and **where** in your own repository.
 
 
#### 1. What to include <a name="step1"></a>

This part is not really hard. When having a look to the source code of [Leaflet for R](https://rstudio.github.io/leaflet/) site it can be seen this chunk:
 
```html
<head>
  <!--code-->
  
  <script src="libs/jquery/jquery.min.js"></script>
  <meta name="viewport" content="width=device-width, initial-scale=1" />
  <link href="libs/bootstrap/css/flatly.min.css" rel="stylesheet" />
  <script src="libs/bootstrap/js/bootstrap.min.js"></script>
  <script src="libs/bootstrap/shim/html5shiv.min.js"></script>
  
  ...
  <!--more libraries-->
  ...
  
  <link href="libs/rstudio_leaflet/rstudio_leaflet.css" rel="stylesheet" />
  <script src="libs/leaflet-binding/leaflet.js"></script>
  
  <!--code-->
</head>
```

So now we have it! The only thing to remember is that we need **to load the libraries from the leaflet server (`https://rstudio.github.io/leaflet`)**, meaning that we have to prepend that url to the libraries in our installation:

```html
  <script src="https://rstudio.github.io/leaflet/libs/jquery/jquery.min.js"></script>
  <meta name="viewport" content="width=device-width, initial-scale=1" />
  <link href="https://rstudio.github.io/leaflet/libs/bootstrap/css/flatly.min.css" rel="stylesheet" />
  
  ...
  <!--more libraries-->
  ...
  
  <link 	href="https://rstudio.github.io/leaflet/libs/rstudio_leaflet/rstudio_leaflet.css" rel="stylesheet" />
  <script src= "https://rstudio.github.io/leaflet/libs/leaflet-binding/leaflet.js"></script>
```

You can have a look of my implementation on [`./_includes/leaflet.html`](https://github.com/dieghernan/dieghernan.github.io/blob/master/_includes/leaflet.html).

#### 2.Where to include <a name="step2"></a>

This a little bit more complicated, depending on the structure of your Jekyll template. 
The code chunk should be included in the `<head>` section of your page, so you would need to find where to put it. In the case of **Beautiful Jekyll** it is on [`./_includes/head.html`](https://github.com/dieghernan/dieghernan.github.io/blob/master/_includes/head.html).

So now you just have to paste in the `<head>` the code that you got on [step 1](#step1).

{: .box-note}
<i class="fa fa-star"></i> **Pro tip:** For a better performance of the site, include these libraries only when you need it. In my case, I added a custom variable in my YAML front matter for those posts with a leaflet map, `leafletmap: true`. Go to [step 4](#step4) for a working example.

### The RStudio part 


#### 3. Creating the leaflet map <a name="step3"></a>

Now it's time to create a leaflet map with **RStudio**. I just keep it simple for this post, so I took the first example provided in [Leaflet for R - Introduction](https://rstudio.github.io/leaflet/)


```r
library(leaflet)

m <- leaflet() %>%
  addTiles() %>%  # Add default OpenStreetMap map tiles
  addMarkers(lng=174.768, lat=-36.852, popup="The birthplace of R")
m  # Print the map
```



It is assumed that you are [creating a post with **RStudio**](https://rmarkdown.rstudio.com/authoring_quick_tour.html#rendering_output), so the code presented above should be embedded in an `.Rmd` file.

#### 4. Set up the YAML front matter <a name="step4"></a>

Before knitting your `.Rmd`, you have to set up the [YAML front matter](https://bookdown.org/yihui/rmarkdown/markdown-document.html). Here it is **essential** to set up the option `always_allow_html: yes`, as well as `output: github_document`. As an example, this post was created with the front matter:

```
---
layout: post
title: Leaflet, R, Markdown, Jekyll and GitHub
subtitle: Make it work in 6 steps - a short tutorial
tags: [R,leaflet,Jekyll, html, maps]
linktormd: true
leafletmap: true
always_allow_html: yes
output: github_document
---
```
We are almost there! Now "Knit" your code and get the corresponding `.md`file.

### The Markdown part 

#### 5. Modifying the `.md`file  <a name="step5"></a>
*Update: Depending on how you render your file this step may not be neccesary.*

Have a look to the `.md` code that you have just created. Although not displayed in the preview, you can see in the file itself a chunk that looks like this:

```html
<!--html_preserve-->

  <script type="application/json"data-for="htmlwidget-7ab57412f7b1df4d5773">
    {"x":{"options":
      ...
      "jsHooks":[]}
  </script>
<!--/html_preserve-->
```
Actually that chunk is your leaflet map, created with **RStudio**. You can't see it now because you are previewing a `markdown` file in your local PC, and the libraries installed in [step 1](#step1) are installed on GitHub, but we would solve it later.

Now you just need to paste this piece of code before that chunk:
```html
<!--html_preserve-->
<div id="htmlwidget-7ab57412f7b1df4d5773" style="width:100%;height:216px;" class="leaflet html-widget"></div>
  <script type="application/json"data-for="htmlwidget-7ab57412f7b1df4d5773">
  ...

```

{: .box-warning}
<i class="fa fa-exclamation-triangle"></i> **Warning:**  Be sure that the widget id (`7ab57412f7b1df4d5773` in the example) is the same in the `<div>` and in the `<script>` part. If not your map would not load.

The `style="width:100%; height:216px;` part controls the actual size of the leaflet widget. In this case, the map would adapt to the width of the page with a fixed height of 216px. I put [some examples](#extra) at the end of the post of different size options so you can have a look and see which one is more suitable for your needs.

#### 6. Publish your post <a name="step6"></a>

Now you just have to publish your post as usual!! If everything has been properly set, when Jekyll builds your post it would include the libraries in the header and make the magic happens, just like this:


```
## Error in file(con, "rb"): cannot open the connection
```








{: .box-warning}
<i class="fa fa-exclamation-triangle"></i> **Warning:** Have you checked the YAML front matter of your `.md` file? Have another look, specially if you have followed my [Pro tip](#step2).


### Gallery: Size of a leaflet map <a name="extra"></a>

{: .box-note}
**A note: **For a complete understanding of this section it is recommended to access it on multiple devices, so you can see the different behavior on different screens. Google Chrome allows you to simulate different devices [(more info here)](https://developers.google.com/web/tools/chrome-devtools/device-mode/).

Let's start creating a new leaflet map:

```r
map <- leaflet(options = leafletOptions(minZoom = 1.25, maxZoom = 8)) %>%
  addTiles() %>%
  setMaxBounds(-200, -90, 200, 90) %>%
  setView(-3.56948,  40.49181, zoom = 3) %>%
  addEasyButton(easyButton(
    icon = "fa-globe",
    title = "World view",
    onClick = JS("function(btn, map){ map.setZoom(1.25); }")
  )) %>%
  addEasyButton(easyButton(
    icon = "fa-crosshairs",
    title = "Locate Me",
    onClick = JS("function(btn, map){ map.locate({setView: true}); }")
  ))
```

#### Fixed size
With these examples you can see how to control the absolute size of the leaflet map. The disadvantage of this method is that the size would be fixed for all the devices, so maps sized for smartphones or tables wouldn't look as nice in laptops, etc. and vice versa.

##### Example 1: 672x480px
Fixed size in pixels. By default in my machine is `html "width:672px;height:480px;"`, so if i want to keep it the next `html <div>' should be included:

```html
<div id="htmlwidget-xxxxxxxxxxxxxxxx" style="width:672px; height:480px;" class="leaflet html-widget"></div>
```

```
## PhantomJS not found. You can install it with webshot::install_phantomjs(). If it is installed, please make sure the phantomjs executable can be found via the PATH variable.
```

```
## Warning in normalizePath(f2): path[1]="webshot27275c08a52a.png": No such file or directory
```

```
## Warning in file(con, "rb"): cannot open file 'webshot27275c08a52a.png': No such file or directory
```

```
## Error in file(con, "rb"): cannot open the connection
```

##### Example 2: 200x300px
Let's go narrow and long with `html "width:200px;height:300px;"`:

```html
<div id="htmlwidget-xxxxxxxxxxxxxxxx" style="width:200px; height:300px;" class="leaflet html-widget"></div>
```


```
## PhantomJS not found. You can install it with webshot::install_phantomjs(). If it is installed, please make sure the phantomjs executable can be found via the PATH variable.
```

```
## Warning in normalizePath(f2): path[1]="webshot2727761128b3.png": No such file or directory
```

```
## Warning in file(con, "rb"): cannot open file 'webshot2727761128b3.png': No such file or directory
```

```
## Error in file(con, "rb"): cannot open the connection
```

#### Dynamic size

**Recommended option.** These maps would adapt to the width of your screen, no matter what device you are using. Additionally, you can adapt the aspect ratio to different sizes.

##### Example 3: 16:9
Most common aspect ratio for televisions and computer monitors. Note that the value `html 56.25%` is just the result of dividing 9 by 16.

```html
<div id="htmlwidget-xxxxxxxxxxxxxxxx" style="position: relative; width: 100%;padding-top: 56.25%;" class="leaflet html-widget"></div>
```

```
## PhantomJS not found. You can install it with webshot::install_phantomjs(). If it is installed, please make sure the phantomjs executable can be found via the PATH variable.
```

```
## Warning in normalizePath(f2): path[1]="webshot2727376ed74b.png": No such file or directory
```

```
## Warning in file(con, "rb"): cannot open file 'webshot2727376ed74b.png': No such file or directory
```

```
## Error in file(con, "rb"): cannot open the connection
```

##### Example 4: 4:3
"Old"" aspect ratio for televisions and computer monitors.

```html
<div id="htmlwidget-xxxxxxxxxxxxxxxx" style="position: relative; width: 100%;padding-top: 75%;" class="leaflet html-widget"></div>
```

```
## PhantomJS not found. You can install it with webshot::install_phantomjs(). If it is installed, please make sure the phantomjs executable can be found via the PATH variable.
```

```
## Warning in normalizePath(f2): path[1]="webshot272719ba77dd.png": No such file or directory
```

```
## Warning in file(con, "rb"): cannot open file 'webshot272719ba77dd.png': No such file or directory
```

```
## Error in file(con, "rb"): cannot open the connection
```



##### <i class="fa fa-star"></i> Example 5: 10:7
Suitable for all devices. My personal choice.

```html
<div id="htmlwidget-xxxxxxxxxxxxxxxx"  style="position: relative; width: 100%;padding-top: 70%;" class="leaflet html-widget"></div>
```

```
## PhantomJS not found. You can install it with webshot::install_phantomjs(). If it is installed, please make sure the phantomjs executable can be found via the PATH variable.
```

```
## Warning in normalizePath(f2): path[1]="webshot27275343e8b1.png": No such file or directory
```

```
## Warning in file(con, "rb"): cannot open file 'webshot27275343e8b1.png': No such file or directory
```

```
## Error in file(con, "rb"): cannot open the connection
```