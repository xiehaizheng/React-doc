# 一、React的特点
    1、采用组件化模式、声明式编程，提高开发效率及组件复用率。
    2、在React Native中可以使用React语法进行移动端开发。
    3、使用虚拟DOM+优秀的diffing算法，尽量减少与真实DOM的交互。
## 1、关于虚拟DOM
1、本质是Object类型的对象（一般对象）；
2、虚拟DOM比较“轻”，真实DOM比较“重”，因为虚拟DOM是React内部在用，无需真实DOM上那么多属性。
3、虚拟DOM最终会被React转化为真实DOM，呈现在页面上。
## 2、JSX--javaScript XML
1、react定义的一种类似于XML的js扩展语法：js+XML；
2、本质是React.createElement(component,props,...children)方法的语法糖
3、作用：用来简化创建虚拟DOM；
    a、写法：var ele = <h1>hello</h1>
    b、注意1：他不是字符串，也不是HTML/XML标签
    c、注意2：他最终产生的就是一个js对象
4、标签名任意：HTML标签或其他标签
## 3、jsx语法规则
1、定义虚拟DOM时，不要写引号；
2、标签中混入JS表达式时，要用{};
3、样式的类名指定不要用class，要用className；
4、内联样式，要用style={{key:value}}的形式去写；
5、只有一个根标签；
6、标签必须闭合；
7、标签首字母
（1）若小写字母开头，则将标签转化为html中同名的元素，若html中无该标签，会报错；
（2）若大写字母开头，react就去渲染对应的组件，若组件没有定义，则会报错；
```
const myId = "xxxx"
const data = 'hello'
// 1、创建虚拟DOM
const VDOM = (
    <div>
        <h2 className="title" id={myId}>
            <span style={{ color:'white', fontSize:'29px'}}>{ data }</span>
        </h2>
    </div>
)
// 2、渲染虚拟DOM到页面
ReactDOM.render(VDOM, document.getElementById('test')) // 在html页面中有Id为test的容器。
```
# 二、React面向组件编程
## 2.1 基本理解和使用
### 2.1.1 函数式组件 和 类式组件
函数式组件：用函数定义的组件（适用于简单组件的定义）；
类式组件：用类定义的组件，适用于复杂组件的定义。
### 2.1.2 函数式组件
```
// 1、创建函数式组件
    function MyComponent() {
        console.log(this); // 此处的this是undefined，因为babel编译后开启了严格模式
        return <h2>用函数定义的组件：函数式组件</h2>
    }
// 2、渲染组件到页面
ReactDOM.render(<MyComponent/>,docoment.getElementById('test'))

/*
    执行ReactDOM.render()之后发生了什么？
    1、react解析组件标签，找到了 MyComponent 组件。
    2、发现组价是使用函数定义的，随后调用该函数，将返回的虚拟DOM转化为真实DOM，随后呈现在页面中。
*/
```
### 2.1.3 类式组件
```
// 1、创建类式组件
class MyComponent extends React.Component {
    render() {
        // render 是放在哪里的？--MyComponent的原型对象上，供实例使用 
        // render中的this是谁？--MyComponent的实例对象（MyComponent组件实例对象）
        console.log('render中的this', this)
        return <h2>类式组件：适用于复杂组件的定义</h2>
    }
}
// 2、渲染组件到页面
ReactDOM.render(<MyComponent/>, document.getElementById('test'))
/*
    执行ReactDom.render()之后
    1、React解析组件标签，找到了MyComponent组件。
    2、发现组件是用类定义的，随后new出来该类的实例，并通过该实例调用到原型上的render方法。
    3、将render返回的虚拟DOM转为真实DOM，随后呈现在页面中。
*/
```
## 2.2 组件实例的三大核心属性：state/props/refs
### 2.2.1 state
```
class Weather extends React.Component {
    state = { isHot: true}
    
    changeWeather = () => { 
        // 由于changeWeather是作为onClick的回调，所以不能通过实例调用，是直接调用
        // 类中的方法默认开启了局部严格模式，所以changeWeather中的this为undefined
        const { isHot } = this.state
        this.setState({isHot:!isHot})
    }
    
    render() {
        const { isHot } = this.state
        return <h2 onClick={ this.changeWeather }>今天天气很{ isHot ? '炎热':'寒冷' }</h2>
    }
}

ReactDOM.render(<Weather/>，document.getElementById('test'))
```
### 2.2.2 props
```
class Person extends React.Component {
    static propTypes = {
        name: PropTypes.string.isRequired,
        age: PropTypes.number,
        sex: PropTypes.string,
    }
    
    static defaultProps = {
        sex: '男',
        age: 18
    }

    render() {
        const  { name, age, sex } = this.props
        return (
            <ul>
                <li>姓名：{name}</li>
                <li>性别：{sex}</li>
                <li>年龄：{age}</li>
            </ul>
        )
    }
}

const p = {name: 'jerry', age: 18, sex:'女'}
ReactDOM.render(<Person {...p} />,document.getElementById('test'))
```
### 2.2.3 refs 与事件处理
```
// 1、字符串形式的ref--不推荐使用（效率不高）
class Demo extends React.Component {
    showData = ()=> {
        const { input1 } = this.refs
        alert(input1.value)
    }
    render(
        <div>
            <input ref="input1" type="text" placeholder="点击按钮提示数据"/>
            <button onClick={this.showData}>按钮</button>
        </div>
    )
}
ReactDOM.render(<Demo/>,document.getElementById('test'))

// 2、回调函数形式的ref
class Demo extends React.Component {
    showData = ()=> {
        const { input1 } = this
        alert(input1.value)
    }
    render(
        <div>
            <input ref={currentNode => this.input1 = currentNode } type="text" placeholder="点击按钮提示数据"/>
            <button onClick={this.showData}>按钮</button>
        </div>
    )
}
ReactDOM.render(<Demo/>,document.getElementById('test'))

// 3、createRef--目前最为推荐
class Demo extends React.Component {
    /* React.createRef调用后可以返回一个容器，该容器可以存储被ref所标识的节点,该容器是专人专用的*/
    myRef = React.createRef()
    myRef2 = React.createRef()
    showData = ()=> {
        alert(this.myRef.current.value)
    }
    render(
        <div>
            <input ref={this.myRef} type="text" />
            <button onClick={this.showData}>按钮</button>
            <input ref={this.myRef2} type="text" />
        </div>
    )
}
ReactDOM.render(<Demo/>,document.getElementById('test'))
```
## 2.3 受控组件（推荐使用） 与 非受控组件
### 2.3.1 非受控组件
```
// 非受控组件
class Demo extends React.Component {
    // 表单提交的回调
    handleSubmit = (event)=> {
        event.preventDefault() // 阻止表单提交
        const { username, password } = this
        alert(`你输入的用户名是：${username.value}，你输入的密码是：${password.value}`)
    }
    render(
        <form onSubmit={this.handleSubmit}>
            用户名：<input  ref={c => this.username = c} type="text" name="username"/>
            密码：<input ref={c => this.password = c} type="password" name="password"/>
            <button>登录</button>
        </div>
    )
}
ReactDOM.render(<Demo/>,document.getElementById('test'))
```
### 2.3.2 受控组件
```
// 受控组价
class Demo extends React.Component {
    // 初始化状态
    state = {
        username: '',
        password: ''
    }
    // 保存表单数据到状态中
    saveFormData = (dataType)=> {
        return (event)=> {
            this.setState({[dataType]: event.target.value})
        }
    }
    // 表单提交的回调
    handleSubmit = (event)=> {
        event.preventDefault() // 阻止表单提交
        const { username, password } = this.state
        alert(`你输入的用户名是：${username}，你输入的密码是：${password}`)
    }
    render(
        <form onSubmit={this.handleSubmit}>
            用户名：<input onChange={this.saveFormData('username')} type="text" name="username"/>
            密码：<input onChange={this.saveFormData('password')} type="password" name="password"/>
            <button>登录</button>
        </div>
    )
}
ReactDOM.render(<Demo/>,document.getElementById('test'))
```
### 2.3.3 高阶函数：
    如果一个函数复合线面2个规范中的任何一个，那该函数就是高阶函数
    1、若A函数，接收的参数是一个函数，那么A就可以称之为高阶函数
    2、若A函数，调用的返回值依然是一个函数，那么A可以称之为高阶函数
    常见的高阶函数：Promise、setTimeout、arr.map()等等
### 2.3.4 函数柯里化：
    通过函数调用继续返回函数的形式，实现多次接收参数最后统一处理的函数编码形式。
    fun(1)(2)(3)
## 2.4 组件的生命周期
### 2.4.1 旧生命周期（V16.8.4）
![](assets/16662751904295.jpg)
![image](https://user-images.githubusercontent.com/47715787/222882563-447c9a0c-aa15-4b3f-a648-4b41151bf3b7.png)
左面为挂载，右面为更新。
componentWillReceiveProps在初始化时不被调用，父组件改变时才会调用。
卸载组件：由ReactDOM.unmountComponentAtNode()触发。

常用生命周期：
1、componentDidMount():一般在这个钩子中做一些初始化的事情，例如：开启定时器、发送网络请求、订阅消息。
2、componentWillUnmount():一般在这个钩子中做收尾的事情，例如：关闭定时器、取消订阅消息。
3、render()。
### 2.4.2 新的生命周期（V17）
![](assets/16662778467489.jpg)
1、初始化阶段：由ReactDOM.render()触发--初次渲染
    1.constructor()
    2.getDerivedStateFromProps
    3.render()
    4.componentDidMount()
2、更新阶段:由组件内部this.setSate()或父组件重新render触发
    1.getDerivedStateFromProps
    2.shouldComponentUpdate()
    3.render()
    4.getSnapshotBeforeUpdate--更新之前存储快照
    5.componentDidUpdate()
3、卸载组件:由ReactDOM.unmountComponentAtNode()触发
    componentWillUnmount()
### 2.4.3 重要的钩子
1、render:初始化渲染或更新渲染调用
2、componentDidMount：开启监听，发送ajax请求
3、componentWillUnmount:做一些收尾工作，如：清理定时器
### 2.4.4 即将废弃的钩子
componentWillMount
componentWillReceiveProps
componentWillUpdate
16版本使用会出现警告，17版本需要加上UNSAFE_前缀才能使用。

## 2.5 DOM的diffing算法
最小粒度是标签
### 2.5.1 key的作用&key最好不要用index
1、虚拟DOM中key的作用
    1)简单的说：key是虚拟对象的标识，在更新显示时key起着极其重要的作用。
    2)详细的说：当状态数据发生改变时，根据【新数据】生成【新的虚拟DOM】，随后进行新【新虚拟DOM】与【旧虚拟DOM】的diff比较，比较规则如下：
    a.旧虚拟DOM中找到了与新虚拟DOM相同的key：
        若虚拟DOM中内容没变，直接使用之前的真实DOM；内容发生改变，生成新的真实DOM，随后替换掉页面之前的真实DOM
    b.旧虚拟DOM中未找到与新虚拟DOM相同的key：根据数据创建新的真实DOM，随后渲染到页面。
2、用index作为key可能会引发的问题
    a.对数据进行：逆序添加、逆序删除等破坏顺序的操作：会产生没有必要的真实DOM更新 ==》 界面效果没问题，但效率低
    b.若结构中还包含输入类DOM：会产生错误DOM更新==》界面有问题
    c.若仅用于列表渲染展示，使用index作为key是没有问题的。


# 三、React脚手架--(create-react-app)
npm install -g create-react-app
create-react-app hello-react
cd hello-react
npm start
# 四、React ajax
XHR Fetch
## 1、XHR
```
<!--使用 XHR 发送一个 json 请求一般是这样-->
var xhr = new XMLHttpRequest();
xhr.open('GET', url);
xhr.responseType = 'json';
// 成功的回调
xhr.onload = function() {
    console.log(xhr.response);
}
// 失败的回调
xhr.onerror = function() {
    console.log('error')
}

xhr.send();
```
## 2、Fetch
使用 Fetch 后，看起来好一点
```
fetch(url).then(function(response) {
    return response.json();
}).then(function(data){
    console.log(data);
}).catch(function(e) {
    console.log(e);
})
```
使用ES6的箭头函数后
```
fetch(url).then(response => return respose.json())
.then(data => console.log(data))
.catch(e => console.log(e))
```
使用async/await优化
```
// 注：这段代码如果想运行，外面需要包一个 async function
try {
    let response = await fetch(url);
    let data = await response.json(); // response.json() 返回的仍是Promise
    console.log(data);
} catch (e) {
    console.log(e)
}
```
# 五、路由
## 1、路由组件的基本使用
1、明确好界面中的导航区、展示区
2、导航区的a标签改为Link标签
    <Link to="/xxxx">Demo</Link>
3、展示区写Route标签进行路径的匹配
    <Route path="/xxx" component={Demo}>
4、<App>的最外层包裹一个<BrowserRouter>或<HashRouter>
## 2、路由组件 与 一般组件
### 2.1 写法不同
一般组件：<Demo />
路由组件：<Route path="/xxx" component={Demo}>
### 2.2 存放位置不同
    一般组件：components
    路由组件：pages
### 2.3 接收到的props不同
    一般组件：写组件标签时，传递了什么，就收到什么
    路由组件：主要接收到了【history】【location】【macth】
    history:
        go: f go()
        goBack: f goBack()
        goForward: f goForward()
        push: f push(path, state)
        replace: f replace(path, state)
    loaction: 
        pathname: '/xxx'
        search: ''
        state: undefined
    match:
        params: {}
        path: '/xxx'
        url: '/xxx'
## 3、NavLink--动态添加类名
```
<NavLink activeClassName="xx"/>
```
### 3.1 NavLink  与 封装NavLink
1、NavLink可以实现路由链接的高亮，通过activeClassName指定样式名
2、标签体内容是一个特殊的标签属性
3、通过this.props.chlidren可以获取标签体内容
## 4、Switch的使用
1、通常情况下，path和component是一一对应关系。
2、Switch可以提高路由匹配效率（单一匹配）
## 5、路由的严格匹配 与 模糊匹配
1、默认使用的是模糊匹配（【输入路径】必须包含【匹配路径】）
2、开启严格匹配：<Route exact={true} path="/about" component={About} />
3、严格匹配不要随便开启，需要再开。有些时候开启会导致无法继续匹配二级路由。
## 6、Redirect的使用
1、一般写在所有路由注册的最下方，当所有路由都无法匹配时，跳转到Redirect指定的路由
```
<Switch>
    <Route path="/aaa" component={Aaa} />
    <Route path="/bbb" component={Baa} />
    <Redirect to="/aaa">
</Switch>
```
## 7、向路由组件传递参数
### 7.1、params参数
    路由连接携带参数：<Link to={`/demo/test/${id}`}>详情</Link>
    注册路由（声明接收）：<Route path="/demo/test/:id" component={Test} />
    接收参数：const {id} = this.props.match.params
### 7.2、传递search参数
    路由连接携带参数：<Link to={`/demo/test/?id=${id}`}>详情</Link>
    注册路由（无需声明接收）：<Route path="/demo/test" component={Test} />
    接收search参数:
    const { search } = this.props.location
    const { id } = qs.parse(search.slice(1))
![](assets/16677213807792.jpg)
需要提前引入库： `import qs from 'querystring'`
```
let obj = { name: 'tom', age: 18} 
qs.stringify(obj) // name=tom&age=18  转换成了erlencoded编码

let str = 'name=tom&age=20'
qs.parse(str) // {name: 'tom', age='20'}
```
### 7.3 向路由组件传递state参数（该参数和state属性无关）在地址栏中无体现
路由连接携带参数：
    `<Link to={{ pathname: '/demo/test', state: { id: obj.id}}}>详情</Link>`
注册路由（声明接收）：
    `<Route path="/demo/test" component={Test} />`
接收state参数：
    `const { id } = this.props.location.state || {}`
    
### 7.4  开启 replace 模式
`<Link  replace={ true } to={{ pathname: '/demo/test', state: { id: obj.id}}}>详情</Link>`
## 8、编程式路由导航
```
// 携带params参数
this.props.history.push(`/home/test/${id}/${title}`)
this.props.history.replace(`/home/test/${id}/${title}`)

// 携带search参数
this.props.history.push(`/home/test?id=${id}&title=${title}`)
this.props.history.replace(``/home/test?id=${id}&title=${title}`)

// 携带state参数
this.props.history.push({
   path: `/home/test`,
   state: {
    id,
    title,
   },
})
this.props.history.replace() 同理
```
## 9、withRouter 的使用
```
import { withRouter } from 'react-router-dom'

class Header extends Component {
    // Header 是一般组件，不能props中找到history、location、macth
}
// withRouter 能接收一般组件，将一般组件加工为路由组件
export default withRouter(Header)
```
## 10、BrowserRouter 与 HashRouter
![](assets/16677249842996.jpg)

# 六、redux
## 1、redux简介
1.1 redux是什么？
    1、redux是一个专门用于做状态管理的JS库（不是react插件库）；
    2、可以用在react、angular、vue等项目中，但是基本与react配合使用；
    3、作用：集中式管理react应用中多个组件共享的状态。
1.2 什么情况下需要使用redux？
    1、某个组件的状态，需要让其他组件可以随时拿到（共享）；
    2、一个组件需要改变另一个组件的状态（通信）；
    3、总体原则：能不用就不用，如果不用比较吃力才考虑使用。
## 2、redux工作流程
redux原理图：
![](assets/16678310373327.jpg)

## 3、redux的三个核心概念
### 3.1 action
    1、动作对象；
    2、包含两个属性：
        type：标识属性，值为字符串，唯一，必要属性
        data：数据属性，值任意类型，可选属性
    3、例子： {type: 'ADD_STUDENT', data: {name: 'tom', age: 18}}
### 3.2 store
    1、将state、action、reducer联系在一起的对象；
    2、如何得到此对象；
        ```
        import { createStore } from 'redux';
        import reducer from './reduces';
        const store = createStore(reducer);
        ```
    3、此对象的功能；
        ```
        getState():得到state
        dispatch(action): 分发action，触发reducer调用，产生新的state
        subscribe(listener):注册监听，当产生了新的state，自动调用
        ```
### 3.3 reducer
    1、用于初始化状态，加工状态；
    2、加工时，根据旧的state和action，产生新的state的纯函数。
## 4、使用redux编写应用
## 5、redux的核心API
## 6、react-redux
### 6.1 react-redux模型图
1、所有的UI组价都应该包裹一个容器组件，他们是父子关系；
2、容器组件是真正和redux打交道的，里面可以随意的使用redux的api。
3、UI组件中不能使用任何redux的api；
4、容器组件会传给UI组件：（1）redux中所保存的状态；（2）用于操作状态的方法；
5、备注：容器给UI传递：状态、操作状态的方法，均通过props传递。
![](assets/16682394744406.jpg)
### 6.2 连接 UI组件 与 容器组件
```
// 引入Count的UI组件
import CountUI from '../components/Count'
// 引入connect用于连接UI组件与redux, need install <npm install react-redux>
import { connect } from 'react-redux'
// 使用connect()()创建并暴露一个Count的容器组件
const countContainer = connect()(CountUI)

export default countContainer
```
同时，需要在App.jsx文件中引入容器组件，即替换CountUI组件.需要在此处引入store。
```
import CountContainer from 'containers/Count/index.jsx'
...
<CountContainer store={store} />
```
### 6.3 react-redux的基本使用
#### 6.3.1 明确两个概念
UI组件：不能使用任何redux的api，只负责页面的呈现、交互等
容器组件：负责和redux通信，将结果交给UI组件。
#### 6.3.2 如何创建一个容器组件---靠react-redux的connect函数
connect(mapStateToProps, mapDispatchToProps)(UI组件)
    mapStateToProps:映射状态，返回值是一个对象
    mapDispatchToProps：映射操作状态的方法，返回值是一个对象
#### 6.3.3 备注
容器组件中的store是靠props传进去的，而不是在容器组件中直接引入；
mapDispatchToProps,也可以是一个对象。
#### 6.3.4 Provider优化
```
import store from './redux/store'
import { Provider } from 'react-redux'

const root = ReactDOM.createRoot(document.getElementById('root'));
root.render(
    <Provider store={store}>
      <App />
    </Provider>
);
```
### 7、一个组件要和redux交互要经过的步骤
1、定义好UI组价，不暴露；
2、引入connect生成一个容器组件，并暴露
    connect(
        state => ({key:value}), // 映射状态
        {key: xxxxAction} // 映射操作状态的方法
    )(UI组件)
### 8、react-redux多个组件数据共享
1、定义一个Person组件，和Count组件通过redux共享数据；
2、为Person组价编写：reducer、action，配置constant常量；
3、重点：Person的reducer和Count的Reducer要使用combineReducers进行合并；合并后的总状态是一个对象；
4、交给store的是总的reducer，最后注意在组件中取出状态的时候，记得取到位。
### 9、react-redux开发者工具的使用
1、yarn add redux-devtools-extension
2、.store中进行配置
import { composeWithDevTools } from 'redux-devtools-extension'
const store = createStore(allReducer,composeWithDevTools(applyMiddleware(thunk)))
## 7、纯函数
1、一类特别的函数：只要是同样的输入（实参），必定得到同样的输出（返回）
2、必须遵守以下一些约束
    不得改写参数数据；（例如不能给实参重新赋值）
    不会产生任何副作用，例如网络请求，输入和输出设备；（不能发生网络请求）；
    不能调用Date.now()或者Math.random()等不纯的方法
3.redux的reducer函数必须是一个纯函数。 
# 七、setState更新状态的两种写法
## 1、setState(stateChange, [callback])--对象式的setState
    1.stateChange为状态改变对象（该对象可以体现出状态的更改）；
    2.callback是可选的回调函数，在状态更新完毕、界面也更新后（render调用后）才被调用；
    ```
    this.setState({count: 2}, () => {
        console.log(this.state.count)
    })
    ```
## 2、setState(updater, [callback])--函数式的setState
    1.updater为返回stateChange对象的函数；
    2.updater可以接收state和props；
    3.callback是可选的回调函数，在状态更新完毕、界面也更新后（render调用后）才被调用；
    ```
    this.setState(
    (state, props) => ({count: state.count + 1}),
    () => {
        console.log(this.state.count)
    })
    ```
## 3、总结
    1、对象式的setState 是 函数式setState的简写方式（语法糖）
    2、使用原则： 
    a.如果新状态不依赖于原状态 ===》 使用对象方式
    b.如果新状态依赖于原状态 ===》 使用函数方式
    c.如果需要再setState()执行后获取最新的数据状态，要在第二个callback函数中读取。
# 八、Hooks
## 1、React Hook/Hooks 是什么？
    react 16.8.0版本增加的新特性、新语法；
    可以在函数组件中使用state以及其他的React特性。
## 2、三个常用的Hook
### State Hook: React.useState()
### Effect Hook: React.useEffect()
### Ref Hook: React.useRef()
### 1、State Hook--使用state
1、State Hook让函数组件也可以有state状态，并进行状态数据的读写操作；
2、语法：`const [xxx, setXxx] = React.useState(initValue)`;
3、useState()说明：
    参数：第一次初始化指定的值在内部做缓存
    返回值：包含两个数组元素，第一个为内部当前状态值，第二个为更新状态值的函数；
4、setXxx() 2中写法：
    setXxx(newValue)：参数为非函数值，直接指定新的状态值，内部用其覆盖原来的状态值；
    setXxx(value => newValue):参数为函数，接收原来的状态值，返回新的状态值，内部用其覆盖原来的状态值。
### 2、Effect Hook--使用生命周期钩子
1、Effect Hook 可以让你在函数组件中执行副作用操作（用于模拟类组件中的生命周期钩子）；
2、React中的副作用操作：
    发ajax请求数据获取；
    设置订阅、启动定时器
    手动更改真实DOM
3、语法说明
```
useEffect(() => {
    // 在此可以执行任何带副作用的操作
    return () => { // 组件卸载前执行
        // 在此做一些收尾工作，比如清除定时器，取消订阅等
    }
}, [stateValue])//如果指定的是[],回调函数只会在第一次render()后执行
```
4、可以把useEffect Hook 看做如下三个函数的组合
componentDidMount()
componentDidUpdate()
componentWillUnmount()
### 3、Ref Hook
1、Ref Hook可以在函数组件中存储/查找组件内的标签或任意其他数据；
2、语法：`const refContainer = React.useRef()`;
3、作用：保存标签对象，功能与React.createRef()一样。
# 九、Fragment--去除多余<div>标签
<Fragment></Fragment>
<></>
作用：可以不用必须有一个真实的DOM根标签了。
# 十、Content
一种组件间通信方式，常用于【祖组件】与【后代组件】间通信
使用：
```
// 1、创建Context容器对象
const XxxContext = React.createContext()
// 2、渲染子组件时，外面包裹XxxContext.Provider,通过value属性给后代组件传递数据：
<XxxContext.Provider value={数据}>
    子组件
</XxxContext.Provider>
// 3、后代组件读取数据
    //第一种方式：仅适用于类组价
static contextType = XxxContext // 声明接收context
this.context // 读取context中的value数据
    //第二种方式：函数组件与类组件都可以
<XxxContext.Consumer>
{
    value => ( // value 就是context中的value数据
        要显示的内容
    )
}
</XxxContext.Consumer>
```
tip：在应用开发中，一般不用context，一般都是用它的封装react插件
# 十一、组件优化--PureComponent

```
 export default class Parent extends Component {
    state = { carName="bcC63"}
    
    changeCar = () => {
        this.setState({})
    }
    
    render() {
        console.log('parent---render')
        const { carName } = this.state;
        return (
            <div className="parent">
                <h3>我是Parent组件</h3>
                <span>车的名字是：{carName}</span><br/>
                <button onClick={this.changeCar}>点我换车</button>
                <Child/>
            </div>
        )
    }
 }
 
 class Child extends Component {
    render() {
        console.log('child---render')
        return (
            <div className="child">
                <h3>我是子组件</h3>
                <span>车的名字是：{this.props.carName}</span>
            </div>
        )
    }
 }
```
## Component的2个问题：
    1、只要执行setState(),即使不改变数据状态，组件也会重新render();
    2、只要当前组件重新render(),就会自动重新render子组件，纵使子组件没有用到父组件的任何数据 ===》效率低
## 效率高的做法：
    只有当组件的state或者props数据发生改变时才重新render()
## 原因：
component中的shouldComponentUpdate()总是返回true
## 解决
办法1：
    重写shouldComponentUpdate()方法
    比较新旧state或者props数据，如果有变化才返回true，否则返回fasle
```
     shouldComponentUpdate(nextProps, nextState) {
        console.log(this.props, this.state); // 目前的props和state
        console.log(nextProps, nextState); // 接下来要变化的目标props，目标state
        return this.state.carName !== nextState.carName;// 但是状态数据多的话就比较麻烦比较起来
     }
// 在子组件中也需要进行上述操作
    shouldComponentUpdate(nextProps, nextState) {
        return this.props.carName !== nextProp.carName;
    }
```
方法2：
    使用PureComponent（纯组件）。PureComponent重写了shouldComponentUpdate(),只有state或者props数据有变化才返回true；
    **tip**：
        只是进行state和props数据的浅比较，如果只是数据对象内部数据发生了改变，返回false
        不要直接修改state数据，而是要产生新的数据
项目中一般使用PureComponent来优化。
```
import { PureComponent } from 'react';
export defalut class xxx extends PureComponent {}
```
# 十二、render props--标签属性的render，相当于Vue的插槽技术 
```
 export default class Parent extends Component {
    render() {
        return (
            <div className="parent">
                <h3>我是Parent组件</h3>
               <!-- <A>hello</A> // hello 是props中的一个属性，用children接收-->
                <A>
                    <B/>
                </A>
            </div>
        )
    }
 }
 
 class A extends Component {
    render() {
        return (
            <div className="child">
                <h3>我是A组件</h3>
                { this.props.chlidren} // 使用该语句才能调用组件B
            </div>
        )
    }
 }
 
 class B extends Component {
    render() {
        return (
            <div className="child">
                <h3>我是B 组件</h3>
            </div>
        )
    }
 }
```
![](assets/16690386028073.jpg)
下方代码仍能实现上述图片效果
```
 export default class Parent extends Component {
    render() {
        return (
            <div className="parent">
                <h3>我是Parent组件</h3>
                <A render={(name) => <B name={name}/>}/>
            </div>
        )
    }
 }
 
 class A extends Component {
    state = {name: 'tom'}
    render() {
        const {name} = this.state;
        return (
            <div className="child">
                <h3>我是A组件</h3>
                { this.props.render(name)} // 使用该语句才能调用组件B
            </div>
        )
    }
 }
 
 class B extends Component {
    render() {
        return (
            <div className="child">
                <h3>我是B 组件</h3>
            </div>
        )
    }
 }
```
总结：
## 如何向组价内部动态传入带内容的结构（标签）？
vue中： 
    使用slot技术，也就是通过组件标签体传入结构： <A><B /></A>;
React中：
    使用children props:通过组件标签体传入结构；
    使用render props：通过组件标签属性传入结构，一般用render函数属性；
## children props
    ```
    <A>
        <B>xx</B>
    </A>
    {this.props.children}
    问题：如果B组件需要A组件内的数据 ==》 做不到
    ```
## render props
```
    <A render={(data)=> <B data={data} />}/>
    A组件：{this.props.render(内部state数据)}
    B组件：读取A组件传入的数据显示 {this.props.data}
```
# 十三、错误边界--Error Boundary--只适用于生产环境
```
export default class Parent extends Component{
    state = {
        hasError: '', // 用于标识子组件是否产生错误
    }
    
    // 当Parent的子组件出现报错时，会触发getDerivedStateFromError调用，并携带错误信息
    state getDerivedStateFromError(error) {
        return {hasError: error}
    }
    
    componentDidCatch() {
        console.log('渲染组件时出错')
        console.log('此处统计错误，反馈给服务器，用于通知编码人员进行bug的解决')
    }
    
    render() {
        return (
            <div>
                <h2>我是Parent组件</h2>
                {this.state.hasError ? <h2>当前网络不稳定，请稍后再试</h2>:<Child/>}
            </div>
        )
    }
}

class Child extends Component{
    state: {
        users: [
            {id: 001, name: '001'},
            {id: 002, name: '002'},
            {id: 003, name: '003'}
        ]
    }
    
    render() {
        return (
            <div>
                {
                    this.state.users.map((user) => {
                        <h3 key={user.id}>{user.name}<h3>
                    });
                }
            </div>
        )
    }
}
```
## 理解
错误边界（Error boundary）：用来捕获后代组件数据，渲染出备用页面；
## 特点
只能捕获后代组件生命周期产生的错误，不能捕获自己组件产生的错误和其他组件在合成事件、定时器中产生的错误
## 使用方式
getDerivedStateFromError配合componentDidCatch
```
static getDerivedStateFromError(Error) {
    //在render之前触发，返回新的state
    return {
        hasError: true,
    }
}

componentDidCatch(error, info) {
    //统计页面的错误，发送请求到后台
    console.log(error, info);
}
```
# 十四、组件通信方式总结
## 1、组件间关系
父子关系
兄弟组件（非嵌套组件）
祖孙组件（跨级组件）
## 几种通信方式
1、props
    children props
    render props
2、消息订阅--发布
    pubs-sub、event等
3、集中式管理：
    redux
4、conText
    生产者-消费者模式
## 比较好的搭配方式
父子关系： props
兄弟组件： 消息订阅发布；集中式管理
祖孙组件：消息订阅发布、集中式管理、conText（开发用的少，封装插件用的多）
# 十五、ReactRouter6
## 1、概述
1、React Router以三个不同的包发布到npm上，分别为：
    1.react-router:路由的核心库，提供了很多的组件、钩子；
    2.react-router-dom:包含react-router所有的内容，并添加一些专门用于DOM的组件，例如<BrowserRouter>等；
    3.react-router-native:包括react-router所有内容，并添加一些专门用于ReactNative的API，例如<NativeRouter>
2、与React Router5.x版本相比，改变了什么？
    1.内置组件的变化，移除<Switch/>,新增<Routes /> ...
    2.语法的变化：component={about} 变为 element={<About/>}...
    3.新增多个hook：useParams、useNavigate、useMatch...
    4.官方明确推荐函数式组件了！！！！！
    。。。。。 
## 2、主要--Component
### 2.1 BrowserRouter
    1、<BrowserRouter />用于包裹整个应用。
    2、示例代码：
    ```
    import React from 'react';
    import ReactDom from 'react-dom';
    imort { BrowserRouter } from 'react-router-dom';
    
    ReactDOM.render(
        <BrowserRouter>
            {/* 整体结构，通常为App组件 */}
        <BrowserRouter />,
        root
    ); 
    ```
### 2.2 HashRouter
    1、说明：作用与<BrowserRouter>一样，但是<HashRouter>修改的是地址栏的hash值；
    2、备注：6版本中与5版本一样；
### 2.3 Routes & Route
1、v6版本中移除了<Switch>,引入了新的替代者<Routes>
2、<Routes /> & <Route />配合使用。
3、<Route />相当于if语句，如果其路径与当前URL匹配，则呈现其对应的组件；
4、<Route caseSensitive />用于指定：匹配时是否区分大小写；
5、当URL发生变化时，<Routes /> 都会查看其所有子<Route>元素以找到最佳匹配并呈现组件；
6、<Route>也可以嵌套使用，且可配合useRoutes()配置路由表，但需要通过<Outlet>组件来渲染其子路由；
7、示例代码：
```
<Route path="/login" element={<Login />}></Route>
// 用于定义嵌套路由，home是一级路由,对应路径/home/*
<Route path="home" element={<Home />}>
    // test1 和 test2 是二级路由
    <Route path="test1" element={<Test1 />}></Route>
    <Route path="test2" element={<Test2 />}></Route>
</Route>

// Route也可以不写element属性，这时就是用于展示嵌套的路由，所以对应的路径是/users/xxx
<Route path="users">
    <Route path="xxx" element={<Demo />}></Route>
</Route>
```
### 2.4 Link
### 2.5 NavLink--高亮
当该NavLink被激活时，添加新的样式。
```
 <NavLink className={({isActive}) => {return isActive ? 'xxx' : ''}} to="/about">about</NavLink>
```
### 2.6 Navigate ---重定向
    1、作用：只要<Navigate />组件被渲染，就会修改路径，切换视图；
    2、replace属性用于控制跳转模式（push、replace。默认是push）
    3、示例代码
    ```
    impot React,{useState} from 'react';
    import { Navigate } from 'react-router-dom';
    
    export default function Home () {
        const [sum, setSum] = useState(1);
        return (
            <div>
                <h3>我是Home组件</h3>
                // 根据sum值决定是否切换视图
                {sum === 1 ? <h4>sum的值为{sum}</h4> : <Navigate to="/about" replace={true}/>}
                <button onClick={() => setSum(2)}>将sum变为2</button>
            </div>
        )
    }
    ```
### 2.7 Outlet
指定路由组件呈现的位置 
### 2.8 向路由组件传递state参数（该参数和state属性无关）在地址栏中无体现
路由连接携带参数：
    `<Link to='/demo/test' state:{{ id: obj.id, title: obj.title, content: obj.content}}>详情</Link>`
注册路由（声明接收）：
    `<Route path="/demo/test" component={Test} />`
接收state参数：
    `const {state:{id,title,content}} = useLocation()`
![](assets/16693844740381.jpg)

## 3、主要-- Hooks
### 3.1 useRoutes--路由表
```
//一般写在routes/index.js
const element = useRoutes([
    {
        path:'/about',
        element:<About/>
    },
    {
        path:'/home',
        element:<Home/>
    },
    {   // 重定向
        path:'/',
        element:<Navigate to="/about"/>
    },
])

// 在需要注册路由的地方
{element}
```
### 3.2 useNavigate--编程式导航
import {useNavigate} from 'react-router-dom'
```
const navigate = useNavigate();

function showDetail() {
    navigate('about', {
        replace: false,
        state: {
            id,
            title,
        }
    })
}
```
实现前进和后退
```
const navigate = useNavigate();

<button onClick={back}>back</button>
<button onClick={forward}>back</button>

function back() {
    navigate(-1)
}
function forward() {
    navigate(1)
}
```
### 3.3 useParams
接收路径中的params参数
```
const {id, title} = useParams();
```
### 3.4 useSearchParams
`detail/id=${id}&title=${title}`
接收路径中的search参数
```
const [search, setSearch] = useSearchParams();
const id = search.get('id');
const title = search.get('title');
```
### 3.5 useLocation
`detail/id=${id}&title=${title}`
接收路径中的search参数
```
const a = useSearchParams();
console.log('@', a)
```  
![](assets/16693840756129.jpg)
 
### 3.6 useMatch--使用不多
接收路径中的params参数
```
const {id, title} = useMatch('完整的路径');
```
### 3.7 useInRouterContext()--不常用
作用：如果组件在<Router>的上下文中呈现，则useInRouterContext钩子返回true，否则返回false。
是否被路由器包裹。
### 3.8 useNavigationType()--不常用
作用：返回当前的导航类型（用户是如何来到当前页面的）
返回值：POP PUSH REPLACE
备注：POP是指在当前浏览器中直接打开这个路由组件（刷新页面）
### 3.9 useOutlet()--不常用
作用：用来呈现当前组件中要渲染的嵌套路由
示例：
```
const result = useOutlet()
console.log(result)
//如果嵌套路由没有挂载，则result为null
//如果嵌套路由已经挂载，则展示嵌套的路由对象
```
### 3.10 useResolvedPath()--不常用
作用：给定一个URL值，解析其中的：path、search、hash值
