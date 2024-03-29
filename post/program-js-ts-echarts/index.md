# Echarts


## 配置项详解

```js
var option = {
    //标题
    title : {
        show:true, //显示策略，默认值true,可选为：true（显示） | false（隐藏）
        text: '主标题', //主标题文本，'\n'指定换行
        link:'', //主标题文本超链接,默认值true
        target: null, //指定窗口打开主标题超链接，支持'self' | 'blank'，不指定等同为'blank'（新窗口）
        subtext: '副标题', //副标题文本，'\n'指定换行
        sublink: '', //副标题文本超链接
        subtarget: null, //指定窗口打开副标题超链接，支持'self' | 'blank'，不指定等同为'blank'（新窗口）
        x:'center' //水平安放位置，默认为'left'，可选为：'center' | 'left' | 'right' | {number}（x坐标，单位px）
        y: 'top', //垂直安放位置，默认为top，可选为：'top' | 'bottom' | 'center' | {number}（y坐标，单位px）
        textAlign: null ,//水平对齐方式，默认根据x设置自动调整，可选为： left' | 'right' | 'center
        backgroundColor: 'rgba(0,0,0,0)', //标题背景颜色，默认'rgba(0,0,0,0)'透明
        borderColor: '#ccc', //标题边框颜色,默认'#ccc'
        borderWidth: 0, //标题边框线宽，单位px，默认为0（无边框）
        padding: 5, //标题内边距，单位px，默认各方向内边距为5，接受数组分别设定上右下左边距
        itemGap: 10, //主副标题纵向间隔，单位px，默认为10
        textStyle: { //主标题文本样式{"fontSize": 18,"fontWeight": "bolder","color": "#333"}
            fontFamily: 'Arial, Verdana, sans...',
            fontSize: 12,
            fontStyle: 'normal',
            fontWeight: 'normal',
        },
        subtextStyle: {//副标题文本样式{"color": "#aaa"}
            fontFamily: 'Arial, Verdana, sans...',
            fontSize: 12,
            fontStyle: 'normal',
            fontWeight: 'normal',
        },
        zlevel: 0, //一级层叠控制。默认0,每一个不同的zlevel将产生一个独立的canvas，相同zlevel的组件或图标将在同一个canvas上渲染。zlevel越高越靠顶层，canvas对象增多会消耗更多的内存和性能，并不建议设置过多的zlevel，大部分情况可以通过二级层叠控制z实现层叠控制。
        z: 6, //二级层叠控制，默认6,同一个canvas（相同zlevel）上z越高约靠顶层。
    },

    xAxis:{

        

        axisLabel: {
            interval: 6,//间隔
            rotate: 35//旋转量

    }


    //提示框，鼠标悬浮交互时的信息提示
    tooltip : {
        trigger: 'item',
        formatter: "{a} <br/>{b} : {c} ({d}%)"
    },
    //图例，每个图表最多仅有一个图例
    legend: {
        orient: 'vertical',
        left: 'left',
        data: ['第一部分','第二部分','第三部分','第四部分']
    },
    // 系列列表,每个系列通过 type 决定自己的图表类型
    series : [
        {
            name: '访问',
            type: 'pie',
            radius : '62%',
            center: ['50%', '65%'],/
            minAngle:'15',
            markPoint: {//标记点
                data: [
                    {type: 'max', name: '最大值'},
                    {type: 'min', name: '最小值'}
                ]
            },
            markLine:{//标记线
                data: [[
                    {coord:[mar.up,0]},//指定线的两个点
                    {coord:[mar.up,mar.top]}
                ]]
            },
            makrArea:{
                itemStyle: {
                    color: 'rgba(255, 173, 177, 0.4)'
                },
                data: [ [{
                    name: '早高峰',
                    xAxis: '07:30'
                }, {
                    xAxis: '10:00'
                }], [{
                    name: '晚高峰',
                    xAxis: '17:30'
                }, {
                    xAxis: '21:15'
                }] ]
            },
            data:[
                {name:"第一部分",value:4},
                {name:"第二部分",value:7},
                {name:"第三部分",value:3},
                {name:"第四部分",value:1},
            ],
            itemStyle: {
                normal:{
                    label:{
                        show:true,
                        formatter: "{b} :\n  {c} \n ({d}%)",
                        position:"inner"
                    }
                }
            }
        }
    ],
};

```

## 实例方法


