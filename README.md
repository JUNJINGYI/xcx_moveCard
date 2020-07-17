##小程序手势滑动卡片的使用说明

#### 概要

基于小程序 Animation 的二次开发

#### 文档

- props

      
| 属性 | 说明 | 类型 | 默认值 | 备注 |
| --- | --- | --- | --- | --- |
| isFront1 | 卡片 1 显示卡片的正反面  | Boolean | true |  |
| isFront2 | 卡片 2 显示卡片的正反面  | Boolean | true |  |
| isFront3 | 卡片 3 显示卡片的正反面 | Boolean | true |  |
| animationData1 | 卡片 1 的左滑动右滑动动画 | Object | {} |  |
| animationData2 | 卡片 2 的左滑动右滑动动画 | Object | {} |  |
| animationData3 | 卡片 3 的左滑动右滑动动画 | Object | {} |  |
| ballTop1 | 卡片 1 距离顶部的距离 | Number | 90 | 根据项目需求自己更改距离 |
| ballTop2 | 卡片 2 距离顶部的距离 | Number | 75 |  |
| ballTop3 | 卡片 3 距离顶部的距离 | Number | 60 |  |
| ballWidth1 | 卡片 1 的宽度 | Number | 680 | 根据项目需求自己更改宽度 |
| ballWidth2 | 卡片 2 的宽度 | Number | 640 |  |
| ballWidth3 | 卡片 3 的宽度 | Number | 605 |  |
| index1 | 卡片 1 的 z-index 层级 | Number | 3 | 层级高的在最外层 |
| index2 | 卡片 2 的 z-index 层级 | Number | 2 |  |
| Index3 | 卡片 3 的 z-index 层级 | Number | 1 |  |


- events

      事件名        | 说明          | 参数
      ------------ | ------------ | -------------
      touchstart1     | 卡片1手指触摸动作开始 | (起始点的坐标X、起始点的坐标Y)
      touchend1       | 卡片1手指触摸后移动   | (手势滑动距离、手势滑动方向)
      touchstart2 | 卡片 2 手指触摸动作开始 | (起始点的坐标 X、起始点的坐标 Y)
      touchend2 | 卡片 2 手指触摸后移动 | (手势滑动距离、手势滑动方向)
      touchstart3 | 卡片 3 手指触摸动作开始 | (起始点的坐标 X、起始点的坐标 Y)
      touchend3 | 卡片 3 手指触摸后移动 | (手势滑动距离、手势滑动方向)

### 使用示例

##### 说明

`translateXX > 0`说明向右滑，`translateXX < 0`说明向左滑

- 卡片 1 第一次滑动触发动画

```
Animation1: function (translateXX) {
    let animation = wx.createAnimation({
        duration: 680,
        timingFunction: 'ease',
    })
    this.animation = animation
    if (translateXX > 0) {
        // 右滑的动画
        this.animation.translateY(0).rotate(20).translateX(translateXX).opacity(0).step()
    } else {
        // 左滑的动画
        this.animation.translateY(0).rotate(-20).translateX(translateXX).opacity(0).step()
    }
        // 滑动至消失的动画
        this.animation.translateY(0).translateX(0).opacity(1).rotate(0).step({duration: 10})
    this.setData({
        animationData1: this.animation.export(),
    })
    setTimeout(() => {
        this.setData({
            // 卡片1在第一次滑动后的距离（滑动到最后一张的位置）
            ballTop1: 60,
            ballLeft1: -302.5,
            ballWidth1: 605,
            index1: 1,
            //  卡片2在第一次滑动后的距离（滑动到第一张的位置）
            ballTop2: 90,
            ballLeft2: -340,
            ballWidth2: 680,
            index2: 3,
            //  卡片3在第一次滑动后的距离（滑动到最中间的位置）
            ballTop3: 75,
            ballLeft3: -320,
            ballWidth3: 640,
            index3: 2,
        })
    }, 300)
}
```

- 卡片 1 手指触摸事件

```
  touchstart1: function (event) {
        touchDotX = event.touches[0].pageX // 起始点的坐标X
        touchDotY = event.touches[0].pageY // 起始点的坐标Y
  },
```

- 卡片 1 移动结束处理动画

```
touchend1: function (event) {
    // 手指离开屏幕时记录的坐标
    let touchMoveX = event.changedTouches[0].pageX
    let touchMoveY = event.changedTouches[0].pageY
    // 起始点的坐标(x0,y0)和手指离开时的坐标(x1,y1)之差
    let tmX = touchMoveX - touchDotX
    let tmY = touchMoveY - touchDotY
    // 两点横纵坐标差的绝对值
    let absX = Math.abs(tmX)
    let absY = Math.abs(tmY)
    //起始点的坐标(x0,y0)和手指离开时的坐标(x1,y1)之间的距离
    let delta = Math.sqrt(absX * absX + absY * absY)
    // 如果delta超过60px（可以视情况自己微调）,判定为手势触发
    if (delta >= 60) {
        // 如果 |x0-x1|>|y0-y1|,即absX>abxY,判定为左右滑动
        if (absX > absY) {
            // 如更tmX<0，即(离开点的X)-(起始点X)小于0 ，判定为左滑
            if (tmX < 0) {
                // 执行左滑动画
                this.Animation1(-500)
                // 如更tmX>0，即(离开点的X)-(起始点X)大于0 ，判定为右滑
            } else {
                // 执行右滑动画
                this.Animation1(500)
            }
            // 如果 |x0-x1|<|y0-y1|,即absX<abxY,判定为上下滑动
        } else {
            // 如更tmY<0，即(离开点的Y)-(起始点Y)小于0 ，判定为上滑
            if (tmY < 0) {
                this.setData({
                    isFront1: !this.data.isFront1,
                })
            // 如更tmY>0，即(离开点的Y)-(起始点Y)大于0 ，判定为下滑
            } else {
                this.setData({
                    isFront1: !this.data.isFront1,
                })
            }
        }
    } else {
        // 手势未触发
    }
    // 让上一张卡片展现正面（如果之前翻转过的话）
    this.setData({
        isFront3: true,
    })
}
```
