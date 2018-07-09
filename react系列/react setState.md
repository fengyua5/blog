## 1. setState的参数及参数类型
相信大家都经常用这个setState这个api，大家第一反应就是一个参数，如this.setState({name:“是是是”})，
但是应注意这个api还有第二个参数，第二个参数是一个function，作用是在我们渲染dom后（也就是真正的改变state.name后）执行后可以调这个callback，比如
```
this.setState({name:'sss'}, () => {
    console.log(this.state.name)
})
```
大家认为这样就结束了，no，第一个参数不仅可以是对象还可以是函数，如果传函数作用是怎么样的呢？
如果第一个参数是函数的话，
可参考[react连接](https://facebook.github.io/react/docs/react-component.html#setstate)
```
this.setState({name:'sss'}, () => {
   console.log(this.state.name)
});
this.setState((prevState, props) => {
   //在此处prevState是最新的state值
   return {name: prevState.name + 'hahah'};
 });
```
大家猜一下最后在render会执行几次？2次？答案是1次，因为setState是batch update(不保证是同步的)
## 2. setState是同步的还是异步的？
官当说明： 不一定同步的，这样的话大家可能又会有疑惑为什么不是同步的呢？恩，这是因为如果我们在一个钩子函数中连续的this.setState，如果是同步的话，这样我们就会不断的re-render,这样的话是无疑是消耗性能的，所以react实行的是不一定同步的策略</br>
![更新策略](https://raw.githubusercontent.com/fengyua5/blog/master/img/state.png)</br>
大家如果想了解的话可以详细的了解一下batch update的机制
## 3. 优化建议
1.在异步请求中尽量不要多次setState，可以merge多个对象一次setState，因为在异步中setState是同步的
2.这样的场景
```
class Example extends React.Component {
  constructor(props) {
	super(props);
	this.state = {
	  value: 0
	}
  }

  componentWillMount() {
	setTimeout(() => {
	  this.setState({
		value: 1
	  });
	  console.log('setTimeout');
	})
  }

  render() {
	console.log('render');
	return (
	  <div>
		公共部分代码...
	  </div>
	)
  }
}
```
大家认为会打印几次？输出顺序是render->render->setTimeout
