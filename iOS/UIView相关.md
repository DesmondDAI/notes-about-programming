# UIView相关

### UIView中的生命周期函数

- `initWithCoder:`：从**nib**（如**Interface Builder**）或本地化数据中初始化时会调用。`UIView`对象所在的视图等级树里已经确定，但`IBOutlet`和`IBAction`还没有。
- `initWithFrame:`：从代码中初始化时会调用
- `drawRect:`：每次`UIView`对象需要展示时（第一次展示，内容更改后展示等等）就会被调用，可能会经常被调用。可以通过`setNeedsDisplay`来激活此函数的调用。
- `awakeFromNib`：在`initWithCoder`被调用后，`IBOutlet`和`IBAction`都准备完毕后调用。
