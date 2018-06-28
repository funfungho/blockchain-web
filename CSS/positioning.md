- `static` - 默认位置
- `relative`
    - 相对于元素原来应该在的位置定位
        - 相对于普通文档流中的位置定位
    - 无论是否移动，元素仍然占据原来的空间，并可能覆盖其他元素
- `absolute`
    - 相对于**最近的非 static 父节点**来定位；若不存在这样的父节点，则相对 `window` 定位
    - 绝对定位使元素的位置与文档流无关，不占据原来空间（原来占据的空间被“回收”）
        - 普通文档流中的元素的布局就像绝对定位的元素不存在时一样
        - 改变绝对定位元素的尺寸不会导致周边其它元素重新定位
    - 可以覆盖其他元素
    - `z-index` 控制框的叠放次序，值越大越靠前
- `fixed`    
    - 相对于 `viewport` 定位
    - 不随窗口滚动而移动
- 非 static 元素用 `top`, `down`, `left`, `right` 设置位置，用 `z-index` 设置层次。
    - 未设置 `z-index` 的非 static 元素会重叠。
- `float` 与 `absolute` 和 `fixed` 不能在一起