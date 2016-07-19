对react-redux的一些理解：

redux是一个state的处理中心

在react中，我们设置不同的组件组合render我们的view

在我们的html中，我们的value值使用this.props中的属性值

关于props： this.props是由当前组件的父级得来的，而我们是没有办法通过修改

chilidren的props ，它是top-down的。而我们也没有必要去操作/修改我们this.props

关于state： state和props都是可以设置初始值/默认值的，它可以用来改变我们所

render的内容。但是一定要注意，我们不要去改变props，改变state是一个很好的做法。

我们给state塞进我们的内容。

在redux中，我们通过dispatch 一个action （也是唯一的形式去操作我们的state）

在我们的redure 中 判断逻辑和操作我们state的内容 最后在生成一个新的store

redux的store实际上是state的集合（这样说可能也不太正确）

单向的数据绑定使得我们在处理表单数据的时候必须这样的流程。

在render 时，我们首先获得当前状态下的state 去判断我们的this.props.user.Id 

等是否有初始值，如果没有，我们直接dispatch一个初始的user对象值。有则可以直接

赋值。

我们在id， name等不同的input当中 ，加入我们的onchange事件。

每当触发了改变，我们都diisaptch一个action去更改我们的state，而redure中判断

最后生成一个新的store，我们获得的this.props也随着改变

我们的value = this.props.user就可以了

.map 可以用来遍历数组输出值
