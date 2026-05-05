<template>
	<div id="app">
		<div class="bg"></div>
		<div class="hello text-white">
			<div class="logo-wrap">
				<img :src="'https://d2h8jcrpwh6m7t.cloudfront.net/tokens/' + (address ? address.toLowerCase() : 'c') + '.png'" alt="">
			</div>
			<p class="symbol">{{symbol}}</p>
			<p class="desc">{{desc}}</p>
			<div class="card base-info">
				<h3 class="zc6">基本信息</h3>
				<ul>
					<li class="item"><label for=""><span class="zcolor2">名称</span></span></label><span class="value">{{name}}</span></li>
					<li class="item"><label for=""><span class="zcolor2">符号</span></label><span class="value">{{symbol}}</span></li>
					<li class="item"><label for=""><span class="zcolor2">精度</span></label><span class="value">{{decimals}}</span></li>
					<li class="item"><label for=""><span class="zcolor2">总量</span></label><span class="value">{{totalSupply.toFixed(0)}}</span></li>
					<li class="item" v-if="['0x5f36739c5cf8e477781b6fb0802d37dc6026fee9'].indexOf(address) < 0">
						<label for=""><span class="zcolor2">合约地址</span></label>
						<span v-if="address" @tap="copy(address)" class="copy-btn zc2"></span>
						<span class="value contract">{{address?hiddenAddress(address):'-'}}</span>
					</li>
				</ul>
			</div>
			<div class="card release-info">
				<h3 class="zc6">发行信息</h3>
				<ul>
					<li class="item">
						<label for=""><span class="zcolor2">官网</span></label>
						<span class="value"><a rel="nofollow" class="website" :href="web=='-'?'':web">{{web?web:'-'}}</a></span>
					</li>
					<li class="item"><label for=""><span class="zcolor2">第三方评级</span></label>
						<span class="value">{{level?level:'-'}}</span>
					</li>
					<li class="item"><label for=""><span class="zcolor2">发行时间</span></label>
						<span class="value">{{ zmoment(time).format("yyyy-MM-DD HH:mm:ss") }}</span>
					</li>
				</ul>
			</div>
			<div class="card report" style="padding-top: 8px; padding-bottom: 8px;">
				<ul>
					<li class="item"><label for=""><span class="zcolor2">举报</span></label><span class="value"><img
								src="data:image/png;base64,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"
								alt="" class="arrow-icon"></span></li>
				</ul>
			</div>
			<view style="height: 17.5rem; background-color: black;"></view>
		</div>
	</div>
</template>

<script>
	var _this;
	export default {
		data() {
			return {
				chain: 366,
				address: "",
				symbol: "",
				name: "",
				decimals: 0,
				totalSupply: 0,
				time: "-",
				desc: "-",
				web: "-",
				level: "-",
			};
		},
		onLoad(options) {
			this.chain = options.chain;
			this.address = options.address;
			if (this.address == 'undefined') {
				if (this.chain == '240601') {
					this.address = '';
					this.symbol = 'CC';
					this.name = '-';
					this.decimals = 18;
					this.totalSupply = 30000000;
					this.time = 1717256667000;
					this.desc = '开源 Ai 智能链';
					this.web = 'https://cchaingov.org/';
					this.level = 'S';
				}
			}
		},
		mounted() {
			_this = this;
			if (this.address) {
				uni.request({
					url: "https://api.cchaingov.org/chain/addrs?flag=" + _this.address,
					method: 'POST',
					header: {
						'content-type': 'application/x-www-form-urlencoded',
					},
					data: {
						chain: _this.chain,
						address: _this.address,
					},
					success: (res) => {
						if (res.data.code == 10000) {
							_this.symbol = res.data.data[0].symbol;
							_this.name = res.data.data[0].name;
							_this.decimals = res.data.data[0].decimals;
							_this.totalSupply = res.data.data[0].totalSupply;
							_this.time = res.data.data[0].time;
							_this.desc = res.data.data[0].desc;
							_this.web = res.data.data[0].web;
							_this.level = res.data.data[0].level;
							console.log(res.data);
						}
					}
				});
			}
		},
		methods: {}
	}
</script>

<style type="text/css">
	uni-page-body {
		background-color: var(--UI-BG-0) !important;
	}
	body,
	html {
		padding: 0;
		margin: 0;
	}

	.bg,
	body,
	html {
		height: 100%;
		background: -webkit-gradient(linear, left top, left bottom, from(#fff), to(#f6f6f6));
		background: linear-gradient(180deg, #fff 0, #f6f6f6)
	}

	.bg {
		position: fixed;
		z-index: -1;
		width: 100%
	}

	#app {
		height: 100%;
		font-family: Avenir, Helvetica, Arial, sans-serif;
		-webkit-font-smoothing: antialiased;
		-moz-osx-font-smoothing: grayscale;
		text-align: center;
		color: #333
	}

	.hello[data-v-0e783dfa] {
		max-width: 768px;
		-webkit-box-sizing: border-box;
		box-sizing: border-box;
		padding: 20px
	}

	h3[data-v-0e783dfa] {
		margin: 40px 0 0
	}

	ul[data-v-0e783dfa] {
		list-style-type: none;
		padding: 0
	}

	li[data-v-0e783dfa] {
		display: inline-block;
		margin: 0 10px
	}

	a[data-v-0e783dfa] {
		color: #42b983
	}

	.hello {
		margin: 0 auto 18px;
		/* background-image: url(https://d2h8jcrpwh6m7t.cloudfront.net/home/imgs/token_bg.png); */
		background-image: url(https://d2h8jcrpwh6m7t.cloudfront.net/home/bg.jpg);
		background-repeat: no-repeat;
		background-size: 100%;
		background-position: top;
		max-width: 420px;
		-webkit-box-sizing: border-box;
		box-sizing: border-box;
		padding-top: 30px;
		padding-bottom: 12px
	}

	.card {
		margin: 18px;
		padding: 15px;
		border-radius: 5px;
		-webkit-box-shadow: 0 4px 15px 0 #f1f2f3;
		box-shadow: 0 4px 15px 0 #373217;
		background: #13141475
	}

	.arrow-icon {
		width: 16px;
		height: 16px
	}

	h3 {
		margin: 0 0 20px
	}

	ul {
		list-style-type: none;
		padding: 0;
		margin: 0
	}

	li {
		margin: 0 5px;
		padding: 10px 0;
		border-bottom: 1px dashed #838383;
		text-align: left
	}

	li:last-child {
		border-bottom: none
	}

	li label {
		font-size: 13px;
		font-weight: 700
	}

	.desc {
		font-size: 12px;
		margin: 0 20px 10px
	}

	.symbol {
		font-size: 16px;
		font-weight: 700;
		margin: 10px 0
	}

	.value {
		margin-top: 4px;
		float: right;
		font-size: 12px;
		max-width: 240px;
		overflow: hidden;
		white-space: nowrap;
		text-overflow: ellipsis
	}

	.copy-btn {
		float: right;
		display: inline-block;
		width: 16px;
		height: 16px;
		background: url(data:image/png;base64,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) no-repeat;
		background-size: 100% 100%;
		margin-top: 2px
	}

	.modal {
		border-radius: 4px;
		padding: 12px;
		text-align: center;
		height: auto;
		color: #fb192d
	}

	.website {
		text-decoration: none;
		color: #1b7fff
	}

	.logo-wrap {
		margin: 0 0 5px;
		text-align: center
	}

	.logo-wrap img {
		width: 60px;
		height: 60px;
		border-radius: 100px;
		border: 1px solid #dcdfe6;
		-webkit-box-shadow: 0 5px 10px 0 #f1f2f3;
		box-shadow: 0 5px 10px 0 #f1f2f3
	}

	@media screen and (max-width:400px) {
		.value {
			max-width: 210px
		}
	}

	@media screen and (max-width:360px) {
		.value {
			max-width: 120px
		}
	}

	.vue-modal-resizer {
		display: block;
		overflow: hidden;
		position: absolute;
		width: 12px;
		height: 12px;
		right: 0;
		bottom: 0;
		z-index: 9999999;
		background: transparent;
		cursor: se-resize;
	}

	.vue-modal-resizer::after {
		display: block;
		position: absolute;
		content: '';
		background: transparent;
		left: 0;
		top: 0;
		width: 0;
		height: 0;
		border-bottom: 10px solid #ddd;
		border-left: 10px solid transparent;
	}

	.vue-modal-resizer.clicked::after {
		border-bottom: 10px solid #369be9;
	}

	.vm--block-scroll {
		overflow: hidden;
		width: 100vw;
	}

	.vm--container {
		position: fixed;
		box-sizing: border-box;
		left: 0;
		top: 0;
		width: 100%;
		height: 100vh;
		z-index: 999;
	}

	.vm--overlay {
		position: fixed;
		box-sizing: border-box;
		left: 0;
		top: 0;
		width: 100%;
		height: 100vh;
		background: rgba(0, 0, 0, 0.2);
		/* z-index: 999; */
		opacity: 1;
	}

	.vm--container.scrollable {
		height: 100%;
		min-height: 100vh;
		overflow-y: auto;
		-webkit-overflow-scrolling: touch;
	}

	.vm--modal {
		position: relative;
		overflow: hidden;
		box-sizing: border-box;

		background-color: white;
		border-radius: 3px;
		box-shadow: 0 20px 60px -2px rgba(27, 33, 58, 0.4);
	}

	.vm--container.scrollable .vm--modal {
		margin-bottom: 2px;
	}

	.vm--top-right-slot {
		display: block;
		position: absolute;
		right: 0;
		top: 0;
	}

	.vm-transition--overlay-enter-active,
	.vm-transition--overlay-leave-active {
		transition: all 50ms;
	}

	.vm-transition--overlay-enter,
	.vm-transition--overlay-leave-active {
		opacity: 0;
	}

	.vm-transition--modal-enter-active,
	.vm-transition--modal-leave-active {
		transition: all 400ms;
	}

	.vm-transition--modal-enter,
	.vm-transition--modal-leave-active {
		opacity: 0;
		transform: translateY(-20px);
	}

	.vm-transition--default-enter-active,
	.vm-transition--default-leave-active {
		transition: all 2ms;
	}

	.vm-transition--default-enter,
	.vm-transition--default-leave-active {
		opacity: 0;
	}

	.vue-dialog {
		font-size: 14px;
	}

	.vue-dialog div {
		box-sizing: border-box;
	}

	.vue-dialog-content {
		flex: 1 0 auto;
		width: 100%;
		padding: 14px;
	}

	.vue-dialog-content-title {
		font-weight: 600;
		padding-bottom: 14px;
	}

	.vue-dialog-buttons {
		display: flex;
		flex: 0 1 auto;
		width: 100%;
		border-top: 1px solid #eee;
	}

	.vue-dialog-buttons-none {
		width: 100%;
		padding-bottom: 14px;
	}

	.vue-dialog-button {
		font-size: inherit;
		background: transparent;
		padding: 0;
		margin: 0;
		border: 0;
		cursor: pointer;
		box-sizing: border-box;
		line-height: 40px;
		height: 40px;
		color: inherit;
		font: inherit;
		outline: none;
	}

	.vue-dialog-button:hover {
		background: #f9f9f9;
	}

	.vue-dialog-button:active {
		background: #f3f3f3;
	}

	.vue-dialog-button:not(:first-of-type) {
		border-left: 1px solid #eee;
	}
</style>
