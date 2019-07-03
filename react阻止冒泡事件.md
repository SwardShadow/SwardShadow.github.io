# react阻止事件冒泡
[toc]
## 事件类型
### 1.合成事件
在jsx中直接绑定的事件，如
```
<a ref="aaa" onClick={(e)=>this.handleClick(e)}>更新</a>
```
这里的handleClick事件就是合成事件。
### 2.原生事件
通过js原生代码绑定的事件，如
```
document.body.addEventListener('click',e=>{
// 通过e.target判断阻止冒泡
    if(e.target&&e.target.matches('a')){
return;
    }
console.log('body');
})
或
this.refs.update.addEventListener('click',e=>{
            console.log('update');
        });
```
## 阻止冒泡事件分为三种情况
### 阻止合成事件间的冒泡，用`e.stopPropagation()`;
```
import React,{ Component } from 'react';
import ReactDOM,{findDOMNode} from 'react-dom';

class Counter extends Component{
constructor(props){
super(props);
this.state = {
count:0,
        }
    }

handleClick(e){
// 阻止合成事件间的冒泡
        e.stopPropagation();

this.setState({count:++this.state.count});
    }

testClick(){
console.log('test')
     }

render(){
return(
<div ref="test" onClick={()=>this.testClick()}>
<p>{this.state.count}</p>
<a ref="update" onClick={(e)=>this.handleClick(e)}>更新</a>
</div>
        )
    }
}

var div1 = document.getElementById('content');

ReactDOM.render(<Counter/>,div1,()=>{});
```
### 阻止原生事件与最外层document上的事件间的冒泡，用
```
e.nativeEvent.stopImmediatePropagation();

import React,{ Component } from 'react';
import ReactDOM,{findDOMNode} from 'react-dom';

class Counter extends Component{
constructor(props){
super(props);

this.state = {
count:0,
        }
    }

handleClick(e){
// 阻止原生事件与最外层document上的事件间的冒泡
        e.nativeEvent.stopImmediatePropagation();

this.setState({count:++this.state.count});
    }

render(){
return(
<div ref="test">
<p>{this.state.count}</p>
<a ref="update" onClick={(e)=>this.handleClick(e)}>更新</a>
</div>
        )
    }

componentDidMount() {
document.addEventListener('click', () => {
console.log('document');
        });
    }
}

var div1 = document.getElementById('content');

ReactDOM.render(<Counter/>,div1,()=>{});
```
### 阻止合成事件与最外层document上的原生事件上的冒泡，通过判断e.target来避免。
```
import React,{ Component } from 'react';
import ReactDOM,{findDOMNode} from 'react-dom';

class Counter extends Component{
constructor(props){
super(props);

this.state = {
count:0,
        }
    }

handleClick(e){
this.setState({count:++this.state.count});
    }
render(){
return(
<div ref="test">
<p>{this.state.count}</p>
<a ref="update" onClick={(e)=>this.handleClick(e)}>更新</a>
</div>
        )
    }

componentDidMount() {
document.body.addEventListener('click',e=>{
// 通过e.target判断阻止冒泡
            if(e.target&&e.target.matches('a')){
return;
            }
console.log('body');
        })
    }
}

var div1 = document.getElementById('content');

ReactDOM.render(<Counter/>,div1,()=>{});
```