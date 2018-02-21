# react-native bugs

### BUG 01 
如图：
![imag](http://p4i4zcg0d.bkt.clouddn.com/1519218516034.jpg)

解决方法：删除ios目录下的build的目录，然后重新`react-native run-ios`

### BUG 02
```
error: bundling failed: Error: While resolving module `react-native-vector-icons/MaterialIcons`, the Haste package `react-native-vector-icons` was found. However the module `MaterialIcons` could not be found within the package. Indeed, none of these files exist:
```

解决方式：
```
// 删除
rm ./node_modules/react-native/local-cli/core/__fixtures__/files/package.json
// 重启
react-native run-ios
```
