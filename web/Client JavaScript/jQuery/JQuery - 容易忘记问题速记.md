## 1. Checkbox是否选中

```javascript
if($("#id").attr("checked")==true) {
    alert(“选中了!");
}
```

## 2. Radio是否选中

```javascript
var val=$('input:radio[name="sex"]:checked').val();
if(val==null){
    alert("什么也没选中!");
} 
```