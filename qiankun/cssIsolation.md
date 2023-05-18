# 1. 样式隔离问题

``` javascript
// qiankun支持的两种样式隔离方案
// 主项目配置时
start({
sandbox:{
    // shaow dom方式隔离
    strictStyleIsolation:true
}
});
// start({
    // sandbox:{
    // 通过改写样式类名的方式隔离
    // experimentalStyleIsolation:true
// }
// });
```
# 2. 用strictStyleIsolation增加了样式隔离 但是如果使用了组件例如modal等 会将 dom节点挂载到shaowdom之外的地方，这样样式也是无法匹配，需要通过改写appendChild和removeChild方法去解决
``` javascript
// 在项目的index.js中
  let originAppendFn = document.body.appendChild.bind(document.body)
  let originRemoveFn = document.body.removeChild.bind(document.body)
function redirectAppendAndRemoveChild(container){
  document.body.appendChild = function (dom){
    const isModal = dom?.firstChild?.className?.indexOf('ant-modal-root') >= 0
    if(isModal){
      // 可以配置成自适应的id选择器
      const ReactApp = document.body.querySelector('#__qiankun_microapp_wrapper_for_react_app__')
      ReactApp.shadowRoot.appendChild(dom)
    } else{
      originAppendFn(dom)
    }
  }
  document.body.removeChild = function (dom){
    const isModal = dom?.firstChild?.className?.indexOf('ant-modal-root') >= 0
    if(isModal){
      // 可以配置成自适应的id选择器
      const ReactApp = document.body.querySelector('#__qiankun_microapp_wrapper_for_react_app__')
      ReactApp.shadowRoot.removeChild(dom)
    } else{
      originRemoveFn(dom)
    }
  }
}
// render时初始化
function render(){
    redirectAppendAndRemoveChild()
}
// 卸载的时候再还原
export async function unmount(props) {
  instance.unmount()
  instance = null
  document.body.appendChild = originAppendFn
  document.body.removeChild = originRemoveFn
}

```