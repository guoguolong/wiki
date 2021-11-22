## less

```less
.iphonex-css() {
  padding-top: constant(safe-area-inset-top); //为导航栏+状态栏的高度 88px
  padding-top: env(safe-area-inset-top); //为导航栏+状态栏的高度 88px
  padding-left: constant(safe-area-inset-left); //如果未竖屏时为0
  padding-left: env(safe-area-inset-left); //如果未竖屏时为0
  padding-right: constant(safe-area-inset-right); //如果未竖屏时为0
  padding-right: env(safe-area-inset-right); //如果未竖屏时为0
  padding-bottom: constant(safe-area-inset-bottom); //为底下圆弧的高度 34px
  padding-bottom: env(safe-area-inset-bottom); //为底下圆弧的高度 34px
}

@supports (bottom: constant(safe-area-inset-top)) or (bottom: env(safe-area-inset-top)) {
  body.iphonex {
    .iphonex-css();
  }
	/* 10px为原来高度 */
  .footer.iphonex-fix {
    padding-bottom: calc(10PX + constant(safe-area-inset-bottom)) !important;
    padding-bottom: calc(10PX + env(safe-area-inset-bottom)) !important;
  }
}
```

编译

>  less mixin.less mixin.css

## scss

```scss
@mixin iphonex-css {
  padding-top: constant(safe-area-inset-top); //为导航栏+状态栏的高度 88px
  padding-top: env(safe-area-inset-top); //为导航栏+状态栏的高度 88px
  padding-left: constant(safe-area-inset-left); //如果未竖屏时为0
  padding-left: env(safe-area-inset-left); //如果未竖屏时为0
  padding-right: constant(safe-area-inset-right); //如果未竖屏时为0
  padding-right: env(safe-area-inset-right); //如果未竖屏时为0
  padding-bottom: constant(safe-area-inset-bottom); //为底下圆弧的高度 34px
  padding-bottom: env(safe-area-inset-bottom); //为底下圆弧的高度 34px
}

@mixin iphonex-support {
  @supports (bottom: constant(safe-area-inset-top)) or (bottom: env(safe-area-inset-top)) {
    body.iphonex {
      @include iphonex-css;
    }
    
		/* 10px为原来高度 */
	  .footer.iphonex-fix {
	    padding-bottom: calc(10PX + constant(safe-area-inset-bottom)) !important;
  	  padding-bottom: calc(10PX + env(safe-area-inset-bottom)) !important;
	  }
  }
}

@include iphonex-support;
```

编译

>  sass mixin.scss mixin.css