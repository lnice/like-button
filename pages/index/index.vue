<template>
  <view class="zan">
    <likeButton :throttle="100" :large="2">
      <button type="success">点赞</button>
    </likeButton>
  </view>
</template>

<script>
  import likeButton from '@/components/like-button/like-button.vue'
	export default {
    components: {
      likeButton
    },
		data() {
			return {
			}
		},
		methods: {
		}
	}
</script>

<style>
  .zan {
    margin: 500rpx 100rpx;
    text-align: center;
  }
</style>
