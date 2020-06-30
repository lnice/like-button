<template>
  <view class="like-button">
    <view class="animate-wrap">
      <view
        class="a-img" 
        v-for="(item,index) in viewList" 
        :key="index" 
        :ref="item.elId"
        :style="{
          'right': site.x || site[0] + 'rpx',
          'bottom': site.y || site[1] + 'rpx'
        }">
        <image :style="{
          'width': imgWidth + 'rpx',
          'height': imgHeight + 'rpx'
        }" mode="widthFix" :src="item.src" :animation="item.animation"></image>
      </view>
    </view>
    <view class="on-button">
      <image :src="src" mode="widthFix" :style="{
        'width': width + 'rpx',
        'height': height + 'rpx'
      }" v-if="!$slots.default" @click="handleClick"></image>
      <view class="el_like_btn" @click="handleClick">
        <slot></slot>
      </view>
    </view>
  </view>
  
</template>

<script>
	export default {
    props: {
      src: {
        type: String,
        default: '/static/logo.png'
      },
      showImgs: { // 显示图标路径
        type: Array,
        default: () => {
          return [
            'https://7669-video-bj26u-1259614424.tcb.qcloud.la/%E7%82%B9%E8%B5%9E.png?sign=e353c2ab58bda3549d2e7fac31f5d3e7&t=1583142034',
            '/static/logo.png',
          ]
        }
      },
      duration: { // 动画效果时间
        type: Number,
        default: 5000
      },
      range: { // x 间隔幅度
        type: Number,
        default: 50
      },
      high: {
        type: Number,
        default: 360
      },
      width: { // 图标宽度
        type: Number || String,
        default: 52
      },
      height: { // 图标高度
        type: Number || String,
        default: 52
      },
      imgWidth: { // 图标宽度
        type: Number || String,
        default: 52
      },
      imgHeight: { // 图标高度
        type: Number || String,
        default: 52
      },
      throttle: { // 点击节流 ms
        type: Number,
        default: 200
      },
      site: { // x y 坐标 [x<Number>, y<Number>]
        type: Array || Object, 
        default: () => {
          return [30, 160] || { x: 30, y: 160 }
        }
      },
      large: { // 是否缩放冒泡
        type: [Number, Boolean],
        default: false
      }
    },
		data() {
			return {
				viewList: [], // 渲染元素
        elId: 0, // 元素渲染id
				oldTime: 0, // 全局时间用于函数节流
        timer: null // 定时器
			}
		},
		methods: {
			handleClick (e) {
				// 函数节流
        let interval = e.timeStamp - this.oldTime
				if(interval < this.throttle) return null;
        this.oldTime = e.timeStamp
        let animation = {}
        // 创建animate配置
        // #ifdef APP-NVUE
				animation = weex.requireModule('animation')
        // #endif
				let randomImg = Math.floor(Math.random() * this.showImgs.length)
				let _item = {
					elId: 'el_likeicon_' + this.elId, // 生成元素ref
					src: this.showImgs[randomImg],	// 随机图标
					animation: animation, // 每个盒子动画
					x: Math.ceil(Math.random() * this.range),	// 方向间距
					q: Math.floor(Math.random() * 2), // 随机方向
				}
        
				// 动画
        let _abs = ['-', '']
        let _dirX = Number(_abs[_item.q] + _item.x)		// 随机的方向和间距
        let _dirY = this.high - Math.random() * 10
        // 生成DOM
				this.elId ++
				this.viewList.push(_item)
        // #ifndef APP-NVUE
        _item.animation = uni.createAnimation({
          duration: this.duration,
          timingFunction: 'ease-out',
        })
        clearTimeout(this.timer)
        this.timer = setTimeout(() => {
          console.log('animation finished.')
          // 完成后事件回调
          this.viewList = []
          this.$emit('finished')
        }, this.duration)
        // #endif
        // 执行动画
        setTimeout(() => {
          let _n = 1
          if (this.large) _n = typeof(this.large) === 'number' ? this.large : 2;
          // #ifndef APP-NVUE
          _item.animation.translateY(-_dirY).translateX(_dirX).scale(_n, _n).opacity(0).step()
          _item.animation = _item.animation.export()
          // #endif
          // #ifdef APP-NVUE
          let el = this.$refs[_item.elId][0];
          clearTimeout(this.timer)
          _item.animation.transition(el, {
            styles: {
              transform: `translate(${_dirX}rpx, -${_dirY}rpx) scale(${_n}, ${_n}])`,
              transformOrigin: 'center center',
              opacity: 0
            },
            duration: this.duration, // ms
            timingFunction: 'ease-out',
            delay: 0 // ms
          }, () => {
            console.log('animation finished.')
            // 完成后事件回调
            this.$emit('finished')
            // 完成动画后在n秒后清空
            clearTimeout(this.timer)
            this.timer = setTimeout(() => {
              this.viewList = []
            }, this.duration)
          })
          // #endif
        }, 0)
        // 点击立即触发组件事件
        this.$emit('handleClick', this.elId)
			}
		}
	}
</script>

<style lang="scss">
	.a-img {
		position: fixed;
	}
</style>
