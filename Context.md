# Context

随着组件层级的递增，如果最下层的组件需要某个 props，但是该 props 肯恩他来自最上层。为了使最下层获取该 props，我们会在各个中间层也传进去这个 props，中间层只是起到传递作用，不会对这个 props 进行更改。这样会使代码看起来很乱。 Context 提供了另外一种传递数据的方式。 Redux 就采用了这种方式，但是它使用了 HOC 进一步封装了 Context。

使用 Context 时的步骤包括:

1. 创建 UserContext，由于创建的 UserContext 是一个 React 组件，所以要用大写。

   ```javascript
   const UserContext = React.createContext();
   ```

2. 使用 UserContext.Provider 组件，将需要用到 UserContext 的组件包含在 UserContext.Provider 组件中。Redux 采用的方法是放在 App 之外。value props 提供了 UserContext 里面的值。

   ```javascript
   class App extends React.Component {
     render() {
       return (
         <div className="app">
           <UserContext.Provider value={this.state.user}>
             <Nav />
             <Body />
           </UserContext.Provider>
         </div>
       );
     }
   }
   ```

3. 使用 UserContext.Consumer 组件，将需要用到 UserContext 的组件包含在 UserContext.Consumer 组件中。这里采用了 `Render Props` 的模式。这个组件一般用在马上要用的组件之外。类似于 react-redux 中的 connect。

   ```javascript
   const UserAvatar = ({ size }) => (
     <UserContext.Consumer>
       {user => (
         <img
           className={`user-avatar ${size || ""}`}
           alt="user avatar"
           src={user.avatar}
         />
       )}
     </UserContext.Consumer>
   );
   ```

## 官网代码示例

```javascript
// 1. 创建 Context
// Context 可以让我们无须明确地传遍每一个组件，就能将值深入传递进组件树。
// 为当前的 theme 创建一个 context（“light”为默认值）。
const ThemeContext = React.createContext("light");

class App extends React.Component {
  render() {
    // 2. 将 Context 应用于子组件
    // <MyContext.Provider value={/* 某个值 */}> 返回一个 React 组件
    // 使用一个 Provider 来将当前的 theme 传递给以下的组件树。
    // 无论多深，任何组件都能读取这个值。
    // 在这个例子中，我们将 “dark” 作为当前的值传递下去。
    return (
      <ThemeContext.Provider value="dark">
        <Toolbar />
      </ThemeContext.Provider>
    );
  }
}

// 中间的组件再也不必指明往下传递 theme 了。
function Toolbar(props) {
  return (
    <div>
      <ThemedButton />
    </div>
  );
}

class ThemedButton extends React.Component {
  // 3a. class 子组件消费 Context
  // 挂载在 class 上的 contextType 属性会被重赋值为一个由 React.createContext() 创建的 Context 对象。
  // this.context 获得 contextType。
  // 指定 contextType 读取当前的 theme context。
  // React 会往上找到最近的 theme Provider，然后使用它的值。
  // 在这个例子中，当前的 theme 值为 “dark”。
  static contextType = ThemeContext;
  render() {
    return <Button theme={this.context} />;
  }
}

// 3b. Render Props 消费 Context
<MyContext.Consumer>
  {value => /* 基于 context 值进行渲染*/}
</MyContext.Consumer>
```
