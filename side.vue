//侧滑删除
<template>
	<view class="liu-slide">
		<view
			class="liu-slide-left"
			:style="'position: relative;left:' + left + 'rpx'"
			@touchstart="touchstart"
			@touchmove="touchmove"
			@touchend="touchend"
		>
			<slot></slot>
		</view>
		<view class="liu-slide-right">
			<view class="space" :style="'height:' + height + 'rpx;'">
				<view>{{ " " }}</view>
			</view>
			<view class="btn-item" :style="'height:' + height + 'rpx;'" @click.stop="clickItem(item)">
				<image
					mode="aspectFill"
					class="delete-icon"
					:src="require('@m/static/imgs/eventReport/event_list_delete.png')"
				/>
				<view class="item-title">删除</view>
			</view>
		</view>
	</view>
</template>

<script>
export default {
	props: {
		//操作按钮数组
		btnList: {
			type: Array,
			default: () => {
				return [
					{
						id: "2",
						name: "删除",
						width: "140rpx",
						bgColor: "#ed656d",
						color: "#FFFFFF",
						fontSize: "28rpx",
					},
				];
			},
		},
		//当前列索引
		index: {
			type: Number,
			require: true,
			default: 0,
		},
		//是否禁用
		disable: {
			type: Boolean,
			default: false,
		},
	},
	data() {
		return {
			x: 0,
			left: 0,
			operation: 0,
			height: 0,
			screenWidth: 0,
		};
	},
	mounted() {
		this.$nextTick((res) => {
			const systemInfo = uni.getSystemInfoSync();
			this.screenWidth = systemInfo.screenWidth;
			this.getBtnWidth();
			this.getListHeight();
		});
	},
	methods: {
		clickItem(e) {
			this.$emit("clickItem", {
				index: this.index,
				id: e.id,
			});
		},
		//重置方法
		reset() {
			this.left = 0;
		},
		getBtnWidth() {
			let view = uni.createSelectorQuery().in(this).select(".liu-slide-right");
			view
				.boundingClientRect((rect) => {
					this.operation = this.px2rpx(rect.width, this.screenWidth);
				})
				.exec();
		},
		getListHeight() {
			let view = uni.createSelectorQuery().in(this).select(".liu-slide-left");
			view
				.boundingClientRect((rect) => {
					this.height = this.px2rpx(rect.height, this.screenWidth) - 24;
				})
				.exec();
		},
		px2rpx(px, screenWidth) {
			return px / (screenWidth / 750);
		},
		touchstart(e) {
			if (this.disable) return;
			this.x = this.px2rpx(e.touches[0].clientX, this.screenWidth);
		},
		touchmove(e) {
			if (this.disable) return;
			let clientX = this.x - this.px2rpx(e.touches[0].clientX, this.screenWidth);
			if (clientX <= 0) this.left = 0;
			else if (this.operation <= clientX) this.left = this.operation * -1;
			else this.left = clientX * -1;
		},
		touchend(e) {
			if (this.disable) return;
			let clientX = this.x - this.px2rpx(e.changedTouches[0].clientX, this.screenWidth);
			this.left = clientX > this.operation / 2 ? this.operation * -1 : 0;
		},
	},
};
</script>

<style lang="scss" scoped>
.liu-slide {
	width: 100%;
	position: relative;
	overflow: hidden;
}

.liu-slide-left {
	width: 100%;
	overflow: hidden;
	background-color: #f5f5f5;
	transition: left 0.2s ease-in-out;
	z-index: 999;
}

.liu-slide-right {
	position: absolute;
	top: 2rpx;
	right: 0;
	z-index: 99;
	display: flex;
	flex-direction: row;
	align-items: center;
	justify-content: flex-end;
	.space {
		position: relative;
		width: 24rpx;
		background: #f5f5f5;
		z-index: 99;
	}
}

.btn-item {
	position: relative;
	width: 140rpx;
	display: flex;
	flex-direction: column;
	align-items: center;
	justify-content: center;
	background: #f53f3f;
	border-radius: 16rpx;
	.delete-icon {
		width: 32rpx;
		height: 32rpx;
	}
	.item-title {
		margin-top: 16rpx;
		font-family: PingFangSC, PingFang SC;
		font-weight: 400;
		font-size: 28rpx;
		color: #ffffff;
		line-height: 28rpx;
	}
}
</style>
