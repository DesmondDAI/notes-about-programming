- 打开plist文件：`/Applications/Xcode.app/Contents/Frameworks/IDEKit.framework/Resources/IDETextKeyBindingSet.plist`

- 添加以下XML：

```xml
<key>Custom</key>
<dict>
  <key>Delete Current Line</key>
  <string>selectLine:, delete:</string>
  <key>Duplicate Line</key>
  <string>selectLine:, copy:, moveToEndOfLine:, insertNewline:, paste:, deleteBackward:</string>
</dict>
```

#### 恭喜！现在可以重启Xcode并进入Key Bindings来设置热键啦~
