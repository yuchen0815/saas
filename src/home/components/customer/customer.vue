<template>
	<div class="all">
		<div class="ui-title" @click.stop="$closePullBox()">
			<span class="ui-title-logo ui-title-logo-service"></span>
			<p>服务</p>
		</div>
		<div class="ui-main">
			<ul class="ui-home-input">
				<li>
					<p>会计人员:</p>
					<Select clearable v-model="form.kuaiji" style="width:200px">
						<Option v-for="item in selectList.kuaiji" :value="item.code" :key="item">{{ item.name }}</Option>
					</Select>
				</li>
				<li>
					<p>审账人员:</p>
					<Select clearable style="width:200px">
						<Option>123123</Option>
					</Select>
				</li>
				<li>
					<p>公司名称:</p>
					<Input placeholder="请输入" v-model="form.name" style="width: 200px"></Input>
				</li>
				<li>
					<Button type="primary" size="large" @click="getAjax">统计</Button>
				</li>
				<li style="float: right;">
					<Button type="primary" size="large" @click="addCustomer('add')">新增客户</Button>
				</li>
			</ul>

			<div class="ui-home-table">
				<div class="ui-home-table-right">
					<Table highlight-row width="10%" height="530" :columns="columns1" :data="content" @on-row-click="changeMenu"></Table>
				</div>

				<div class="ui-home-table-page">
					<div class="ui-home-table-page-right">
						<Page :total="page.totalElements" :current="page.number" show-elevator show-total @on-change="gopage"></Page>
					</div>
				</div>
			</div>
		</div>
		<pullbox></pullbox>
	</div>
</template>

<script>
const log = console.log;
import pullbox from '../widgets/pullbox.vue'
import ajax from 'utils/ajax';
import { mapState, mapActions } from 'vuex'

export default {
	data () {
		return {
			columns1: [
				{
					title: '客户名称',
					key: 'name'
				},
				{
					title: '客户简称',
					key: 'name'
				},
				{
					title: '客户级别',
					key: 'level',
					render: (h, params) => {
						var level = this.selectList.level.map(item=>{
							if(params.row.level === item.code){
								return item.name;
							}
						})
						return h(params.row.level,level)
					}
				},
				{
					title: '登记时间',
					key: 'createDate'
				},
				{
					title: '企业类型',
					key: 'address'
				},
				{
					title: '行业类型',
					key: 'address'
				},
				{
					title: '所属会计',
					key: 'address'
				},
				{
					title: '审账会计',
					key: 'address'
				},
				{
					title: '客户特点',
					key: 'address'
				},
				{
					title: '联系人',
					key: 'address'
				},
				{
					title: '联系人电话',
					key: 'address'
				}
			],
			form:{//客户列表分页
				page:0,
				size:10,
				name:'',//公司名称
				kuaiji:'',
			},
			page:{},
			content:[],//客户列表
			selectList:{}
		};
	},
	components:{pullbox},
	computed: mapState('homeStore', {
        content: state => state.customerList
    }),
	created() {
		this.closeMenu();
		this.getAjax();//获取列表
		ajax.customer_Select()//获取下拉框数据
		.then(rs=>{
			if (rs.success) {
				this.selectList = rs.data;
			} else {
				this.$tip(rs.message);
			};
		})
		.catch(error => {
			this.$tip('请稍候重试');
		});
	},
	methods : {
		...mapActions('homeStore', ['SET_MENU','SET_COMPONENT']),
		changeMenu(row){ //点击每条数据显示pullbox
			this.SET_MENU(true);
			this.SET_COMPONENT(['customerList', row])
		},
		addCustomer(me){ //点击新增客户显示pullbox
			this.SET_MENU(true);
			this.SET_COMPONENT(['customerAdd', me])
		},
		getAjax(){//获取客户列表
			ajax.customer_List(this.form)
			.then(rs => {
				if (rs.success) {
					this.content = rs.data.content;//客户列表
					this.page = rs.data;//客户列表分页
					this.page.content.length <=0 ? this.page.number = 1 : this.page.number = this.page.number+1;
				} else {
					this.$tip(rs.message);
				};
			})
			.catch(error => {
				this.$tip('请稍候重试');
			});
		},
		gopage(page){//分页跳页
			this.form.page = page-1;
			this.getAjax();
		},
        closeMenu(){//关闭pullbox
			this.SET_MENU(false);
		}
	},
}

</script>

<style lang='less' scoped>
@import '../../../styles/style.less';

</style>