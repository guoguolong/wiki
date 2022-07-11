# Flex布局中widh和flex-basic区别

flex-basis只要flex布局中才会产生作用，但是with不是，如果两者共用会有怎么样的变化？ 现在拿了56版本的firfox，IE11，以及68版本的chrome进行了实验：

## 68版本的chrome：

1. 当不设置width和flex-basis时，宽度默认为内容自身的宽度
1. 设置width，不设置flex-basis，宽度正常随着width走，但是当width小于0时，则宽度恢复为自身内容宽度，这个就不放图了
1. 不设置width，设置flex-basis，当flex-basis设置值小于自身内容宽度时，flex-basis不生效，不管是正值还是负值。当flex-basis设置值大于自身内容宽度时，相应宽度也会正常增加。
1. 同时设置width，又设置flex-basis，
* 当flex-basis大于自身内容宽度时，不管width是否设置，flex-basis优先级高。
* 当flex-basis和width都小于自身内容宽度时，flex-basis和width哪个值大，宽度就是那个。
* 当flex-basis设置值小于自身内容宽度，而width大于自身宽度时，则宽度为自身内容宽度。

## 56版本的firfox：
* 1.当不设置width和flex-basis时，宽度默认为内容自身的宽度

* 2.设置width，不设置flex-basis，宽度正常随着width走，但是当width小于0时，则宽度恢复为自身内容宽度

* 3.不设置width，设置flex-basis，当flex-basis设置值小于自身内容宽度时，flex-basis不生效，不管是正值还是负值。当flex-basis设置值大于自身内容宽度时，相应宽度也会正常增加。这个和Chrome是一致。

* 4.同时设置width，又设置flex-basis，当flex-basis大于自身内容宽度时，不管width是否设置，flex-basis优先级高。
  当flex-basis>=0和width都小于自身内容宽度时，宽度任然是自身内容宽度。但当flex-basis小于0时，也就这个属性不会生效，所以也就是width的宽度值。

当flex-basis设置值小于自身内容宽度，而width大于自身宽度时，则宽度为自身内容宽度。这个和chrome也是一样的。

## IE11

* 1.当不设置width和flex-basis时，宽度默认为内容自身的宽度

* 2.设置width，不设置flex-basis，宽度正常随着width走，但是当width小于0时，则宽度恢复为自身内容宽度。

* 3.不设置width，设置flex-basis，当flex-basis只要>=0，宽度都会正常生效。

* 4.同时设置width，又设置flex-basis，当flex-basis大于自身内容宽度时，不管width是否设置，flex-basis优先级高。
  当flex-basis和width都小于自身内容宽度时，只要flex-basis>=0，width就是flex-basis，否则就是width。

## 总结

1. flex-basis的不允许小于0，小于0的话这个属性就相当于不存在，在IE下还会提示错误，所以都是随着width走。
2. 当flex-basis>=0时，chrome下表现参看 **68版本的chrome** 的第4条。
3. firfox只有flex-basis>=0且小于自身内容宽度，width不管设没设，宽度仍然是自身内容宽度，其他和chrome是一致的。
   IE只要flex-basis>=0，宽度就是flex-basis。

## 结论

在flex布局中，永远不要同时使用 width 和 flex-basic。应总是使用 flex-basic