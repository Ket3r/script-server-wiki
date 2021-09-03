_(since version 1.17)_  
In Script server there is a possibility to define custom colors/background images.  
To do it, you have to create a theme file: `conf/theme/theme.css` and (re)define default variables.  

You can find theme examples in [samples/themes/dark](https://github.com/bugy/script-server/tree/master/samples/themes/dark) and [samples/themes/orange](https://github.com/bugy/script-server/tree/master/samples/themes/orange)  
It goes without saying, that you can use these sample themes as your default one.  

All the variables can be found in [web-src/src/assets/css/shared.css](https://github.com/bugy/script-server/blob/master/web-src/src/assets/css/shared.css) file, but usually, you would need to override only those, which are defined in samples  

You can also define custom background images, if you add them to `conf/theme` folder and then reference them in CSS file using `url('../theme/my_image.png')`  


Important note: when you change/add a theme, users have to refresh the page and caches in the browser (in windows + chrome, it's Ctrl + F5, for example)