### rnpm作用

常常需要使用封装的`Android` 或者`IOS`控件，在使用时需要手动导入到工程项目中，很是麻烦，`rnpm` 可以自动链接库到项目工程中

### 安装rnpm
```
npm i rnpm -g
```

### 使用
```
npm i react-native-modules -S

rnpm link react-native-modules
```

### 需要link的库：
```
react-native-vector-icons//字体图标

react-native-image-picker//读入照片

react-native-camera//相机

react-native-linear-gradient//颜色渐变

react-native-search-bar//search bar

react-native-tableview//原生的tableview，可以在list右侧显示索引
```