<template>
</template>
<script>
	var barcode = null;
	export default {
		data() {
			return {
				name: '', //要在扫码界面自定义的内容
				flash: false, //是否打开摄像头
				type: ''

			};
		},
		onLoad(d) {
			var n = d.text;
			this.type = d.type;
			console.log(this.type,'type');
			// if (n) {
			// 	this.name = n;
			// }
			var pages = getCurrentPages();
			var page = pages[pages.length - 1];
			// #ifdef APP-PLUS
			plus.navigator.setFullscreen(true); //全屏
			var currentWebview = page.$getAppWebview();
			this.createBarcode(currentWebview); //创建二维码窗口
			this.createView(currentWebview); //创建操作按钮及tips界面
			// #endif
		},
		
		methods: {
			// 扫码成功回调
			onmarked(type, result) {
				var text = '未知: ';
				switch (type) {
					case plus.barcode.QR:
						text = 'QR: ';
						break;
					case plus.barcode.EAN13:
						text = 'EAN13: ';
						break;
					case plus.barcode.EAN8:
						text = 'EAN8: ';
						break;
				}
				plus.navigator.setFullscreen(false);
				
				
				//兄弟传参
				// this.$eventHub.$emit(this.type, {
				// 	result: result
				// });				
				
				uni.$emit(this.type, {
					result: result
				})
				console.log(this.type,result,'this.type');
				uni.navigateBack({
					delta: 1
				});
				barcode.close();
			},
			// 创建二维码窗口
			createBarcode(currentWebview) {
				//自定义窗口大小
				//条码类型常量数组，默认情况支持QR、EAN13、EAN8类型。 通过此参数可设置扫码识别支持的条码类型（注意：设置支持的条码类型越多，扫描识别速度可能将会降低）
				barcode = plus.barcode.create('barcode', [plus.barcode.QR, plus.barcode.EAN13,plus.barcode.EAN8], {
					top: '0',
					left: '0',
					width: '100%',
					height: '100%',
					scanbarColor: '#1DA7FF',
					position: 'static',
					frameColor: '#1DA7FF'
				});
				barcode.onmarked = this.onmarked;
				barcode.setFlash(this.flash);
				currentWebview.append(barcode);
				barcode.start();
			},
			// 创建操作按钮及tips
			createView(currentWebview) {
				// 创建返回原生按钮
				var backVew = new plus.nativeObj.View('backVew', {
						top: '0px',
						left: '0px',
						height: '40px',
						width: '100%'
					},
					[{
						tag: 'img',
						id: 'backBar',
						src: 'static/images/backBar.png',
						position: {
							top: '2px',
							left: '3px',
							width: '35px',
							height: '35px'
						}
					}]);
				// 创建打开手电筒的按钮
				var scanBarVew = new plus.nativeObj.View('scanBarVew', {
						top: '60%',
						left: '40%',
						height: '10%',
						width: '20%'

					},
					[{
							tag: 'img',
							id: 'scanBar',
							src: 'static/images/scanBar.png',
							position: {
								width: '28%',
								left: '36%',
								height: '30%'
							}
						},
						{
							tag: 'font',
							id: 'font',
							text: '轻触照亮',
							textStyles: {
								size: '10px',
								color: '#ffffff'
							},
							position: {
								width: '80%',
								left: '10%'
							}
						}
					]);
				// 创建展示类内容组件
				var content = new plus.nativeObj.View('content', {
						top: '0px',
						left: '0px',
						height: '100%',
						width: '100%'

					},
					[{
							tag: 'font',
							id: 'scanTitle',
							text: '扫码',
							textStyles: {
								size: '18px',
								color: '#ffffff'
							},
							position: {
								top: '0px',
								left: '0px',
								width: '100%',
								height: '40px'
							}
						},
						{
							tag: 'font',
							id: 'scanTips',
							text: this.name,
							textStyles: {
								size: '14px',
								color: '#ffffff',
								whiteSpace: 'normal'
							},
							position: {
								top: '90px',
								left: '10%',
								width: '80%',
								height: 'wrap_content'

							}
						}

					]);
				backVew.interceptTouchEvent(true);
				scanBarVew.interceptTouchEvent(true);
				currentWebview.append(content);
				currentWebview.append(scanBarVew);
				currentWebview.append(backVew);
				backVew.addEventListener("click", function(e) { //返回按钮
					uni.navigateBack({
						delta: 1
					});
					barcode.close();
					plus.navigator.setFullscreen(false);

				}, false);
				var temp = this;
				scanBarVew.addEventListener("click", function(e) { //点亮手电筒
					temp.flash = !temp.flash;
					if (temp.flash) {
						scanBarVew.draw([{
								tag: 'img',
								id: 'scanBar',
								src: 'static/images/yellow-scanBar.png',
								position: {
									width: '28%',
									left: '36%',
									height: '30%'
								}
							},
							{
								tag: 'font',
								id: 'font',
								text: '轻触照亮',
								textStyles: {
									size: '10px',
									color: '#ffffff'
								},
								position: {
									width: '80%',
									left: '10%'
								}
							}
						]);
					} else {
						scanBarVew.draw([{
								tag: 'img',
								id: 'scanBar',
								src: 'static/images/scanBar.png',
								position: {
									width: '28%',
									left: '36%',
									height: '30%'
								}
							},
							{
								tag: 'font',
								id: 'font',
								text: '轻触照亮',
								textStyles: {
									size: '10px',
									color: '#ffffff'
								},
								position: {
									width: '80%',
									left: '10%'
								}
							}
						])
					}
					if (barcode) {
						barcode.setFlash(temp.flash);
					}
				}, false)

			}
		},
		onBackPress() {
			// #ifdef APP-PLUS
			// 返回时退出全屏
			barcode.close();
			console.log("onBackPress");
			plus.navigator.setFullscreen(false);
			// #endif
		},
		onUnload() {
			
			console.log("onUnload");
			plus.navigator.setFullscreen(false);
		}


	};
</script>

<style scoped>
</style>
