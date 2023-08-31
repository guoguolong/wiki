[toc]

# react native混合开发 从h5页面跳转到app页面

## 项目场景：

一个react native混合开发[安卓app](https://so.csdn.net/so/search?q=安卓app&spm=1001.2101.3001.7020)的项目中，实现点击一个item跳转到第三方的h5页面，在h5页面填报东西后，点击确认按钮，返回app页面。



## 解决方案

h5页面使用webview展示，在点击按钮返回app页面时

### 方案I -  window.postMessage 通信

* 在WebView里的H5代码

```html
//在html中，点击按钮以后，使用window.postMessage()方法
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
</head>
<body>
<button id="open" onclick="message()">start app</button>
</body>
<script>
   function message(){
   		window.postMessage('我发送信息了')
   }
</script>
</html>
```
* 在 React Native端代码
```javascript
//在webview标签中，使用onMessage监听
render(){
  return (
    <WebView
      style={pageStyle.pageWebView}
      ref={'webview'}
      onMessage = {this.onMessage}
      allowFileAccess={true}
      source={{uri: this.state.baseUrl}}
    />
  )
}
//接受h5发送来的消息,使用路由跳转
onMessage = (event) => {
   // 接受h5发送来的消息
  console.log('接收H5发来的消息onMessage', event.nativeEvent.data);
  if (event.nativeEvent.data === '我发送信息了') {
    this.navigation.navigate('PendingApprovalPage', {
      queryParams: this.state.queryParams,
    })
  }
}
```
### 方案II -  injectedJavaScript监听h5页面的按钮点击

在webview中使用injectedJavaScript监听h5页面的点击按钮，点击按钮后，给webview页面postMessage
```javascript
let monitorButton = `
	document.getElementById('open').onclick = function(){
            window.postMessage('我发送信息了')
       }`
render(){
  return (
    <WebView
      style={pageStyle.pageWebView}
      ref={'webview'}
      onMessage = {this.onMessage}
      allowFileAccess={true}
      injectedJavaScript={monitorButton}
      source={{uri: this.state.baseUrl}}
     />
  )
}
```

这种方法弊端在于需要按钮有唯一id或者class 

### 方案III -  配置url scheme，直接通过url跳转

① 在AndroidManifest.xml文件中，添加url scheme配置

```xml
  <activity
      android:name=".MainActivity"
      android:label="@string/app_name"
      android:screenOrientation="portrait"
      android:configChanges="keyboard|keyboardHidden|orientation|screenSize|navigation|locale|uiMode"
      android:windowSoftInputMode="adjustResize">
      <intent-filter android:autoVerify="true">
          <action android:name="android.intent.action.MAIN" />
          <category android:name="android.intent.category.LAUNCHER" />
      </intent-filter>
      <intent-filter>
          <!-- 这里添加配置 -->
          <action android:name="android.intent.action.VIEW"/>
          <category android:name="android.intent.category.DEFAULT"/>
          <category android:name="android.intent.category.BROWSABLE"/>
          <data android:scheme="intent" android:host="myapp"/>
          <!-- 这里结束配置 -->
      </intent-filter>
  </activity>
```

`<data android:scheme="intent" android:host="myapp"/>`
这个配置就是协议地址 可以通过intent://myapp进行访问

② 在app.js中进行配置`<AppContainer uriPrefix='intent://myapp/' />`

```javascript
import AppContainer from './src/config/router'

render() {
  return (
    <View style={{flex: 1, backgroundColor: '#207DD9', paddingTop: StatusBar.currentHeight}}>
      <Loading isVisible={this.props.loading}/>
      //AppContainer 为配置的路由
      <AppContainer uriPrefix='intent://myapp/' />
    </View>
  )
}
```

* router.js

```js
// 栈路由配置
const StackNavigator = createStackNavigator({
  Login: {
    screen: Login
  },
  Page: {
    screen: Page,
    path: 'page',
  }
}, {
  initialRouteName: 'Login',
  defaultNavigationOptions: {
    ...header.titleCenter
  }
})
```

需要跳转的组件路由需要配置path参数，如果我们需要跳转到指定的page页面，使用url：
`intent://myapp/page`， 如果需要携带参数，page的path需要修改为 
```json
Page: {
	screen: Page,
	path: 'page/:id'
}
```

在Page组件中获取参数使用`this.navigation`

>配置用在项目中后，发现跳转到指定页面时，会先跳转到登陆页面（不需要点登录，只是会闪一下这个页面），再跳转到指定的页面。我目前排查的原因是 使用url scheme时会重启配置了这个scheme的activity，所以会先跳到初始化页面，再跳到指定页面。目前解决方案是 在activity的配置中加上android:launchMode=“singleTask”。有看到别人解决方案是 添加一个类似入口的Activity，对原生安卓不太了解，没有理解是什么意思。
