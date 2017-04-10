文件目录 

  

​          timers: 日期控件下处理 30， 31，闰年，平年

​          animation：动画处理效果（暂时只支持线性移动）

​          util: 处理滑屏事件（touchstart， move， end）

​          city: 省市区信息

​          AreaSelection: 地区选择组件主体

​          Datepicker: 日期选择组件主体

​     

组件调用方式 

​    vue：｛

​    

​      日期选择：

​        ｛

​              <Datepicker :minDay= 2016-01-01 :maxDay=2018-01-01" v-on:HandleDatepicker=HandleDatepicker/>

​              

​              minday:最小日期（可填）

​              maxDay:最大日期（可填）

​              HandleDatepicker: 日期选择后的回调函数（必填）

​              

​              

​             ![image](1.png)

​              

​          ｝

​          

​      地区选择：

​        ｛

​        

​              <AreaSelection  v-on:HandleDatepicker=HandleDatepicker"/>

​              HandleDatepicker: 日期选择后的回调函数（必填）

​              ![image](2.png)

​              

​          ｝

​      

​    ｝

​        

 注：组件css基于750宽度，计算rem; 基于es6编写，需babel

​        

​       