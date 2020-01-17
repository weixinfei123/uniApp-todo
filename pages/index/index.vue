<template>
	<view class="content">
		<view class="todo-header" v-if="list.length !== 0">
			<view class="todo-header-left">
				<text class="active-text">{{text}}</text>
				<text>{{listData.length}}条</text>
			</view>
			<view class="todo-header-right">
				<view class="todo-header-right-item" :class="{'active-tab': activeIndex === 0}" @click="tab(0)">全部</view>
				<view class="todo-header-right-item" :class="{'active-tab': activeIndex === 1}" @click="tab(1)">待办</view>
				<view class="todo-header-right-item" :class="{'active-tab': activeIndex === 2}" @click="tab(2)">已完成</view>
			</view>
		</view>
		<view v-if="list.length === 0" class="defauit">
			<view class="img-defauit">
				<image src="../../static/default.png" mode="aspectFit"></image>
			</view>
			<view class="defauit-info">
				<view class="defauit-info-text">您还没有创建任何待办事项</view>
				<view class="defauit-info-text">点击下方+创建一个吧</view>
			</view>
		</view>
		<view class="todo-content" v-else>
			<!-- todo-finsh -->
			<view class="todo-list" :class="{'todo-finsh': item.checked}" v-for="(item, index) in listData" :key="index" @click="finsh(item.id)">
				<view class="todo-list-box">
					<view class="checkout"></view>
				</view>
				<view class="todo-list-content">
					{{item.content}}
				</view>
			</view>
		</view>
		<uni-rate value="2"></uni-rate>
		<view class="creat-todo">
			<text class="iconfont icon-add" :class="{'creat-todo-active': textShow}" @click="creat"></text>
		</view>
		<view class="creat-content" v-if="active" :class="{'creat-show': textShow}">
			<view class="creat-content-box">
				<view class="creat-input">
					<input type="text" v-model="value" placeholder="请输入创建的todo" />
				</view>
				<view class="creat-button" @click="add">
					创建
				</view>
			</view>
		</view>
	</view>
</template>

<script>
	import uniRate from '@/components/uni-rate/uni-rate.vue'
	export default {
		data() {
			return {
				list: [],
				value: '',
				active: false,
				activeIndex: 0,
				text: '全部',
				textShow: false
			}
		},
		components:{
			uniRate
		},
		onLoad() {

		},
		watch: {
			activeIndex: function(val) {
				console.log(val);
				if(val === 0) {
					this.text = '全部';
				}
				if(val === 1) {
					this.text = '待办';
				}
				if(val === 2) {
					this.text = '已完成';
				}
			}
		},
		computed:{
			listData() {
				let list = JSON.parse(JSON.stringify(this.list));
				let newList = [];
				if(this.activeIndex === 0) {
					// this.text = '全部';  不能在计算属性中修改变量的值  watch监听
					return list;
				}
				if(this.activeIndex === 1) {
					// this.text = '待办';
					//checked  false
					list.forEach((item) => {
						if(!item.checked) {
							newList.push(item);
						}
					})
					return newList;
				}
				if(this.activeIndex === 2) {
					// this.text = '已完成';
					//checked true
					list.forEach((item) => {
						if(item.checked) {
							newList.push(item);
						}
					})
					return newList;
				}
			}
		},
		methods: {
			// 打开输入框
			creat() {
				if(this.active) {
					this.close();
				}else {
					this.open();
				}
				
			},
			open() {
				this.active = true;
				this.$nextTick(() => {
					setTimeout(() => {
						this.textShow = true;
					}, 50)
				})
			},
			close() {
				this.textShow = false;
				this.$nextTick(() => {
					setTimeout(() => {
						this.active = false;
					}, 350)
				})
			},
			add() {
				if(!this.value) {
					uni.showToast({
						title: '请输入内容',
						icon: 'none'
					})
					return;
				}
				this.list.unshift({
					content: this.value,
					id: 'id' + new Date().getTime(),
					checked: false
				})
				this.value = '';
				// this.active = false;
				this.close();
			},
			finsh(id) {
				console.log(id);
				let index = this.list.findIndex((item) => {
					return item.id === id;
				})
				this.list[index].checked = !this.list[index].checked;
				
			},
			tab(index) {
				this.activeIndex = index;
			}
		}
	}
</script>

<style>
	@import "../../common/icon.css";
	page {
		background: red;
	}
	.todo-header {
		position: fixed;
		left: 0;
		top: 0;
		width: 100%;
		padding: 0 15px;
		display: flex;
		align-items: center;
		color: #333333;
		font-size: 12px;
		height: 45px;
		background: #FFFFFF;
		box-shadow: -1px 1px 5px 0 rgba(0,0,0,0.1);
		box-sizing: border-box;
		z-index: 100;
	}
	.todo-header-left {
		width: 100%;
		color: #333333;
	}
	.todo-header-right {
		display: flex;
		flex-shrink: 0;
	}
	.todo-header-right-item {
		padding: 0 5px;
		color: #333333;
	}
	.active-tab {
		color: #007AFF;
	}
	.active-text {
		color: #007AFF;
		font-size: 14px;
		padding-right: 10px;
	}
	.todo-content {
		position: relative;
		padding-top: 50px;
		padding-bottom: 100px;
	}
	.todo-list {
		position: relative;
		display: flex;
		align-items: center;
		padding: 15px;
		color: #0c3854;
		font-size: 14px;
		border-radius: 10px;
		background: #cfebfd;
		margin: 15px;
		overflow: hidden;
		box-shadow: -1px 1px 5px 1px rgba(0,0,0,0.1), -1px 2px 1px 0 rgba(255,255,255) inset;
	}
	.todo-list::after {
		position: absolute;
		content: '';
		left: 0;
		top: 0;
		bottom: 0;
		width: 5px;
		background: #007AFF;
	}
	.todo-list-box {
		padding-right: 15px;
	}
	.checkout {
		width: 20px;
		height: 20px;
		border-radius: 50%;
		background: #FFFFFF;
		box-shadow: -1px 1px 5px 1px rgba(0,0,0,0.1);
	}
	.todo-finsh .checkout {
		background: #eee;
		position: relative;
	}
	.todo-finsh .checkout::after {
		position: absolute;
		content: '';
		width: 10px;
		height: 10px;
		top: 0;
		left: 0;
		right: 0;
		bottom: 0;
		margin: auto;
		background: #CFEBFD;
		border-radius: 50%;
		box-sizing: 0 0 2px 0 rgba(0,0,0,0.2) inset;
	}
	.todo-finsh .todo-list-content {
		color: #999999;
	}
	.todo-finsh.todo-list::before {
		content: '';
		position: absolute;
		top: 0;
		bottom: 0;
		left: 40px;
		right: 10px;
		height: 2px;
		margin: auto 0;
		background: #bdcdd8;
	}
	.todo-finsh.todo-list::after {
		background: #ccc;
	}
	.creat-todo {
		display: flex;
		align-items: center;
		justify-content: center;
		position: fixed;
		bottom: 20px;
		left: 0;
		right: 0;
		width: 50px;
		height: 50px;
		border-radius: 50%;
		background: #deeff5;
		margin: 0 auto;
		box-shadow: -1px 1px 5px 2px rgba(0,0,0,0.1), -1px 1px 5px 2px rgb(255,255,255) inset;
	}
	.icon-add {
		font-size: 30px;
		color: #C0C0C0;
	}
	.creat-content {
		position: fixed;
		bottom: 95px;
		left: 20px;
		right: 20px;
		transition: all 0.3s;
		opacity: 0;
		transform: scale(0) translateY(200%);
	}
	.creat-show {
		opacity: 1;
		transform: scale(1) translateY(0);
	}
	.creat-content-box {
		display: flex;
		align-items: center;
		padding: 0 15px;
		padding-right: 0;
		border-radius: 50px;
		background: #DEEFF5;
		box-shadow: -1px 1px 2px 1px rgba(0,0,0,0.1), -1px 1px 1px 0 rgb(255,255,255) inset;
		z-index: 2;
	}

	.creat-input {
		width: 100%;
		padding-right: 15px;
		color: #add816;
	}
	.creat-button {
		display: flex;
		font-size: 14px;
		justify-content: center;
		align-items: center;
		flex-shrink: 0;
		width: 80px;
		height: 50px;
		border-radius: 50px;
		background: #FFFFFF;
		box-shadow: -2px 0px 2px 1px rgba(0,0,0,0.1);
	}
	.creat-content::after {
		content: '';
		position: absolute;
		left: 0;
		right: 0;
		margin: 0 auto;
		width: 20px;
		height: 20px;
		background: #DEEFF5;
		bottom: -8px;
		transform: rotate(45deg);
		box-shadow: 1px 1px 5px 2px rgba(0,0,0,0.1);
		z-index: -1;
	}
	.creat-content-box::after {
		content: '';
		position: absolute;
		left: 0;
		right: 0;
		margin: 0 auto;
		width: 20px;
		height: 20px;
		background: #DEEFF5;
		bottom: -8px;
		transform: rotate(45deg);
	}
	.defauit {
		padding-top: 50px;
	}
	.img-defauit {
		display: flex;
		justify-content: center;
	}
	.iimg-defauit image {
		width: 100%;
	}
	.defauit-info {
		text-align: center;
		font-size: 14px;
		color: #CCCCCC;
	}
	.icon-add {
		transition: transform 0.3s;
	}
	.creat-todo-active {
		transform: rotate(135deg);
	}
</style>
