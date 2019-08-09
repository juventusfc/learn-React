# 代码分割

对你的应用进行代码分割能够帮助你“懒加载”当前用户所需要的内容，能够显著地提高你的应用性能。尽管并没有减少应用整体的代码体积，但你可以避免加载用户永远不需要的代码，并在初始加载的时候减少所需加载的代码量。

代码分割主要方法有:

1. `import()`

   ```javascript
   // moduleA.js
   const moduleA = "Hello";

   export { moduleA };
   ```

   ```javascript
   // App.js
   import React, { Component } from "react";

   class App extends Component {
     handleClick = () => {
       import("./moduleA")
         .then(({ moduleA }) => {
           // Use moduleA
         })
         .catch(err => {
           // Handle failure
         });
     };

     render() {
       return (
         <div>
           <button onClick={this.handleClick}>Load</button>
         </div>
       );
     }
   }

   export default App;
   ```

2. `React.lazy` 以及 `suspense`

   ```javascript
   const OtherComponent = React.lazy(() => import("./OtherComponent"));

   function MyComponent() {
     return (
       <div>
         <Suspense fallback={<div>Loading...</div>}>
           <OtherComponent />
         </Suspense>
       </div>
     );
   }
   ```

3. 错误边界

   ```javascript
   import MyErrorBoundary from "./MyErrorBoundary";
   const OtherComponent = React.lazy(() => import("./OtherComponent"));
   const AnotherComponent = React.lazy(() => import("./AnotherComponent"));

   const MyComponent = () => (
     <div>
       <MyErrorBoundary>
         <Suspense fallback={<div>Loading...</div>}>
           <section>
             <OtherComponent />
             <AnotherComponent />
           </section>
         </Suspense>
       </MyErrorBoundary>
     </div>
   );
   ```
