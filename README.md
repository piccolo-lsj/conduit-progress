# conduit-progress

> canvans绘制的管道进度条
## 安装

> npm install conduit-progress --save
## 使用
> import conduit-progress from "conduit-progress"
``` html
<conduit-progress :percent="70"/>
```

## 参数

``` js 
    props: {
        width: {
            type: Number,
            default: 200,
        },
        height: {
            type: Number,
            default: 200,
        },
        //管道进度百分比
        percent: {
            type: Number,
            default: 70,
        },
        //管道半径
        radius: {
            type: Number,
            default: 10,
        },
        //拐角半径
        cornerRadius: {
            type: Number,
            default: 70,
        },
        //管道边框颜色
        borderColor: {
            type: String,
            default: "#0CCDA3",
        },
    },
```

### git
> https://github.com/Devil-lsj/addGit.git
### npm
> https://www.npmjs.com/package/conduit-progress
