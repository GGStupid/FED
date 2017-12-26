
当鼠标滑过1个含有子`div`的`div`时，如果在父`div`和子`div`之间来回滑动时，如果父`div`绑定了`mouseover`和``mouseout``事件的话，会频繁的相互促发，如果这个时候你绑定了一些动画，浏览器会出现闪动的现象，这时候把``mouseover``和``mouseout``换成`mouseenter`和`mouseleave`就好了。

`mouseover`与`mouseenter`

不论鼠标穿过被选元素或其子元素，都会触发 `mouseover` 事件。
只有在鼠标穿过被选元素时，才会触发 `mouseenter` 事件。

`mouseout`与`mouseleave`

不论鼠标离开被选元素还是任何子元素，都会触发 `mouseout` 事件。
只有在鼠标离开被选元素时，才会触发 `mouseleave` 事件。

