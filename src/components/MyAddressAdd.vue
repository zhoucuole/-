<template>
<div class="addAddress">
	<headerBox :flag="'back'"
	           :title="'添加收货地址'"></headerBox>
	<div class="main-body">
		<div class="name">
			<label for="name">收货人</label>
			<input v-model="name"
			       name="name"
			       type="text"
			       maxlength="20"
			       placeholder="请输入收货人的名称" />
		</div>
		<div class="phone">
			<label for="phone">联系电话</label>
			<input v-model="phone"
			       name="phone"
			       type="text"
			       maxlength="20"
			       placeholder="请输入联系电话" />
		</div>
		<div class="place">
			<div @click="showChange"
			     class="place-title">
				<span>所在地区</span>
				<span v-if="!region">请选择<i class="iconfont icon-youbian"></i></span>
				<span v-else>{{region.sheng.name+region.shi.name+region.qu.name}}<i class="iconfont icon-youbian"></i></span>
			</div>
			<textarea v-model="detail"
			          class="place-detail"
			          placeholder="请输入详细地址 不少于5个字符"></textarea>
		</div>
		<div @click="addAddress"
		     class="btn save-address">保存地址</div>
	</div>

	<!--地址选择-->
	<addressSelect :show.sync="show_address"
	               @submit="regionSubmit"></addressSelect>
</div>
</template>

<script>
import headerBox from './commons/headerBox.vue'
import addressSelect from './commons/addressSelect.vue'
export default {
	name: 'myAddressAdd',
	components: {
		headerBox,
		addressSelect
	},
	data() {
		return {
			name: '', //收货人姓名
			phone: '', //收货人手机
			show_address: false, //显示地址选择
			region: false, //省市区
			detail: '', //详细地址
		}
	},
	methods: {
		//显示地址选择组件
		showChange() {
			this.show_address = true
		},
		//记录region变量
		regionSubmit(obj) {
			console.log(obj)
			this.region = obj
		},
		//添加地址
		addAddress() {
			if (!this.region.sheng) { //非空判定
				this.$store.dispatch('toast', {
					message: '请选择省',
					icon: '2'
				})
				return
			}
			if (!this.region.shi) {

				this.$store.dispatch('toast', {
					message: '请选择市',
					icon: '2'
				})
				return
			}
			if (!this.region.qu) {
				this.$store.dispatch('toast', {
					message: '请选择区',
					icon: '2'
				})
				return
			}
			this.$http({
					url: this.$store.state.domain + '/user/add_address',
					method: 'GET',
					params: {
						key: window.localStorage.access_token,
						consignee: this.name,
						phone: this.phone,
						province: this.region.sheng.id,
						city: this.region.shi.id,
						district: this.region.qu.id,
						address: this.detail,
					}
				})
				.then(res => {
					console.log(res.data)
					if (res.data.code == '200') {
						this.$store.dispatch('toast', {
							message: res.data.msg,
							icon: '1',
						})
						setTimeout(() => {
							this.$router.go(-1)
						}, 1500)

					} else {
						this.$store.dispatch('toast', {
							message: res.data.msg,
							icon: '2',
						})
					}
				})
		}
	}
}
</script>

<style scoped lang="less">
.addAddress {
    min-height: 100vh;
    background: #f3f3f3;
	overflow-x: hidden;
}

.save-address {
    position: absolute;
    bottom: 0;
    left: 0;
    height: 4.4rem;
    border-radius: 0;
    width: 100%;
}

.main-body {
    .name,
    .phone {
        background: white;
        height: 4.5rem;
        border-bottom: 1px solid #f3f3f3;
        font-size: 1.6rem;
        color: #222222;
        display: flex;
        justify-content: space-between;
        align-items: center;
        padding: 0 1.2rem;
        & > label {
            flex: 0 0 9.5rem;
        }
        & > input {
            flex: 1 1 auto;
            font-size: 1.6rem;
        }
    }
    .place {
        margin-top: 0.8rem;
        background: white;
        .place-title {
            height: 4.5rem;
            display: flex;
            justify-content: space-between;
            align-items: center;
            font-size: 1.6rem;
            color: #222222;
            padding: 0 1.2rem;
            i {
                margin-left: 1rem;
            }
        }
        .place-detail {
            resize: none;
            border: none;
            padding: 0.8rem 3vw;
            height: 10rem;
            width: 94vw;
            font-size: 1.6rem;
        }
    }
}
</style>
