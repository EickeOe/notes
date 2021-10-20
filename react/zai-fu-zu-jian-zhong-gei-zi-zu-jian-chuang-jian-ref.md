# 在父组件中给子组件创建ref

在Discord的FE社区中遇到的问题：

&#x20;im creating a drag/drop component base on jquery ui. as you can see in my code I have to wrap props.children with `<div>` in order to gain ref on the root element. how do I change my code if I want to remove \<div> and use the children root element instead as my droppable?, also what is the difference between ref and itemRef?

```jsx
import * as React from 'react';

export interface IDroppableProps {
  children:React.ReactNode;
  className?:string;
  model?:any;
  onJqDrop?:(item:any,container:any)=>any;
  accept?:any;
}

export function Droppable (props: IDroppableProps) {
    const ref = React.useRef<HTMLDivElement>();

    React.useEffect(()=>{
      ref && ref.current && (ref.current["model"] = props.model);
    },[props.model]);

    React.useEffect(()=>{
      ref && ref.current && $(ref.current).droppable({
        accept: props.accept,
        tolerance: "pointer",
        drop:(event, ui)=>{
          const draggable = ui.draggable[0];
          const droppable = event.target as HTMLElement;
          props.onJqDrop && props.onJqDrop(draggable && draggable["model"] || null, droppable && droppable["model"] || null);
        }
      })
    },[ref.current])

    return (
      <div ref={ref} className={props.className}>
        {props.children}
      </div>
    );
}
```

解答：

```jsx
const ref = React.createRef()
class D extends React.Component {
    render(){
        const children = React.Children.map(this.props.children, child => React.cloneElement(child, {ref}))
        return <React.Fragment>{children}</React.Fragment>        
    }
}
```

1. 使用`React.createRef`创建`ref`.
2. 使用`React.cloneElement`来复制子组件，然后传递`ref`给子组件

