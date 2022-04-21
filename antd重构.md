# **Antd 重构**

## 构建

1. 每一次构建使用 package.json，然后发布时用 _package.json

   

## 文档

* 修改 theme/static/template.html 
  ```js
  return ‘/index-cn/';
  ## 改成 
  return '/components/overview-cn/';
  ```

* site/theme/Content/MainContent.jsx

  ```js
  // line 42增加
  data = [...props.picked['docs/react'], ...props.picked['components']];
  ```

* site/theme/template/Layout/Header/Navigation.tsx  // header的菜单精简

  ```js
  
        {/* <Menu.Item key="docs/resources">
          <Link to={utils.getLocalizedPathname('/docs/resources', isZhCN, location.query)}>
            <FormattedMessage id="app.header.menu.resource" />
          </Link>
        </Menu.Item>
        {showTechUIButton && (
          <Menu.Item key="tech-ui">
            <a href="https://techui.alipay.com" target="__blank" rel="noopener noreferrer">
              TechUI
            </a>
          </Menu.Item>
        )} */}
        {/* {isZhCN && typeof window !== 'undefined' && window.location.host.indexOf('gitee') === -1 && (
          <Menu.Item key="mirror">
            <a href="https://ant-design.gitee.io">国内镜像</a>
          </Menu.Item>
        )}
        {additional} */}
  ```

* site/theme/template/Layout/Header/index.tsx // header的菜单精简

```js
// <Select
            //   key="version"
            //   className="version"
            //   size="small"
            //   defaultValue={antdVersion}
            //   onChange={this.handleVersionChange}
            //   dropdownStyle={this.getDropdownStyle()}
            //   getPopupContainer={trigger => trigger.parentNode}
            // >
            //   {versionOptions}
            // </Select>,
            // <Button
            //   size="small"
            //   onClick={this.onLangChange}
            //   className="header-button header-lang-button"
            //   key="lang-button"
            // >
            //   <FormattedMessage id="app.header.lang" />
            // </Button>,
            // <Button
            //   size="small"
            //   onClick={this.onDirectionChange}
            //   className="header-button header-direction-button"
            //   key="direction-button"
            // >
            //   {this.getNextDirectionText()}
            // </Button>,
            // <More key="more" {...sharedProps} />,
            // <Github key="github" responsive={responsive} />,
```





保留
	index.tsx 
	* icon : 仅仅 "图标"文档
	* overview： 仅仅"组件总览"文档

删除
	__tests__ 测试
	style

聚合到 common包里
	* _util
	* locale : locale-provider 依赖之
	* locale-provider： LocaleReceiver 

* @aurum/pfe-ui
* @aurum/pfe-icons
* @aurum/antd-common
