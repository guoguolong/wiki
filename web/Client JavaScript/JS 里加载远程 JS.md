```js
function loadjs(src, func) {
    var oScript = document.createElement('script');
    oScript.type = 'text/javascript';
    oScript.src = src;
    var body = document.getElementsByTagName('body').item(0);
    body.appendChild(oScript);
    oScript.onload = function() {
        func();
    }
};
```