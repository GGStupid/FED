## node里new  Date()与js里new Date()差8小时

在node里`new date()`,取的是UTC时间

在js里`new date()`,
根据[MDN](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Date)，`new date()`如果没有输入任何参数，则Date的构造器会依据系统设置的当前时间来创建一个Date对象。

整个地球分为二十四时区，每个时区都有自己的本地时间。在国际无线电通信场合，为了统一起见，使用一个统一的时间，称为通用协调时(UTC, Universal Time Coordinated)

服务器采用的时UTC时间，而我们电脑本地的时间时东八区，也就是utc+8的时间。当然差了8个小时


[格林尼治标准时间](https://baike.baidu.com/item/%E6%A0%BC%E6%9E%97%E5%B0%BC%E6%B2%BB%E6%A0%87%E5%87%86%E6%97%B6%E9%97%B4/586530?fr=aladdin)（旧译格林威治平均时间或格林威治标准时间；英语：GreenwichMeanTime，GMT）是指位于英国伦敦郊区的皇家格林尼治天文台的标准时间，因为本初子午线被定义在通过那里的经线。

理论上来说，格林尼治标准时间的正午是指当太阳横穿格林尼治子午线时（也就是在格林尼治时）的时间。由于地球在它的椭圆轨道里的运动速度不均匀，这个时刻可能和实际的太阳时相差16分钟。

地球每天的自转是有些不规则的，而且正在缓慢减速。所以，格林尼治时间已经不再被作为标准时间使用。现在的标准时间──协调世界时（UTC）──由[原子钟](https://baike.baidu.com/item/%E5%8E%9F%E5%AD%90%E9%92%9F)提供。
