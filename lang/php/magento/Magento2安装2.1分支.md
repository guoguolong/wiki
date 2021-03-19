和安装develop分支类似（[假设域名是magento2.mo.me](http://xn--magento2-wb4mt52am1kjj7bhb4g.mo.me)）： A. 配置nginx B. 执行http://magento2.mo.me/setup (必须加上setup) 它会帮助你检查环境是否满足，比如各子目录可写权限：

```
/projects/magento2/app/etc
/projects/magento2/var
/projects/magento2/pub/media
/projects/magento2/pub/static
```

C. 安装translation插件 如果提示： ... but these conflict with your requirements or minimum-stability.. 应修改composer.json：

> "minimum-stability": "alpha" 为合适的值，如： "minimum-stability": "dev"