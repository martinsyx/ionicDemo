# 时间定向

以小时为单位选择需要定向的时间段，根据选择的时间段定向

  - 默认构建一个矩阵数据 7天*24小时
  - 多种快速选择标签获得特定时间段的选择


### 使用指南

导入组件
```sh
 var app = new Vue({
.......
components: {
			"time-interval":timeInterval
		}
})
```

在页面中使用组件
```sh
<time-interval v-bind:day-array = 'data' ref="timeInterval"></time-interval>
```

获得选择的定向数据
```sh
var data=this.$refs.timeInterval.getIntervalData();
```

### Plugins
| name | README |
| ------ | ------ |
| :day-array = 'data' | 给组件传入选择值，空数据全出一个空数组 |
| ref="timeInterval" | 对于组件的引用，以获取组件对象 |
| getIntervalData()| 获得选择的定向数据 |



 
默认数据格式
```sh
var data=[[false,true,false,false,false,false,false,false,false,false,false,false,false,false,false,false,false,false,false,false,false,false,false,false],
[false,true,false,false,false,false,false,false,false,false,false,false,false,false,false,false,false,false,false,false,false,false,false,false],
[false,true,false,false,false,false,false,false,false,false,false,false,false,false,false,false,false,false,false,false,false,false,false,false],
[false,true,false,false,false,false,false,false,false,false,false,false,false,false,false,false,false,false,false,false,false,false,false,false],
[false,true,false,false,false,false,false,false,false,false,false,false,false,false,false,false,false,false,false,false,false,false,false,false],
[false,true,false,false,false,false,false,false,false,false,false,false,false,false,false,false,false,false,false,false,false,false,false,false],
[false,true,false,false,false,false,false,false,false,false,false,false,false,false,false,false,false,false,false,false,false,false,false,false]
]
```