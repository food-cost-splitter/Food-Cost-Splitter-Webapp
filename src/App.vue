<template>
	<v-app>
		<v-toolbar app>
			<v-toolbar-title class="headline text-uppercase">
				<span>Cost Splitter</span>
			</v-toolbar-title>
			<v-spacer></v-spacer>
			<v-toolbar-items class="hidden-sm-and-down">
				<v-btn v-for="(button, ind) in buttons" :key="ind" flat @click="button.click()">{{button.title}}</v-btn>
			</v-toolbar-items>
			<v-menu class="hidden-md-and-up">
				<v-toolbar-side-icon slot="activator"></v-toolbar-side-icon>
				<v-list>
					<v-list-tile v-for="(button, ind) in buttons" :key="ind" @click="button.click()">
						<v-list-tile-content>
							<v-list-tile-title>{{button.title}}</v-list-tile-title>
						</v-list-tile-content>
					</v-list-tile>   
				</v-list>
			</v-menu>
		</v-toolbar>

		<v-content>
			<router-view/>
		</v-content>
		<v-dialog v-model="modalOpen" max-width="400">
			<v-card v-if="modalOpen">
				<v-card-title>
					<h2>Orders</h2>
				</v-card-title>
				<v-list dense two-line>
					<div v-for="(order, ind) in dataList" :key="ind">
						<v-list-tile v-on:click="loadOrder(order.data)" @click="drawer = false">
							<v-list-tile-content>
								<v-list-tile-title>
									{{order.data.orderInfo.name}}
								</v-list-tile-title>
								<v-list-tile-sub-title>
									${{order.data.orderInfo.deliveryTotal}} delivery
								</v-list-tile-sub-title>
							</v-list-tile-content>
							<v-list-tile-action>
								<v-btn @click="deleteOrder(order)" fab small color="error"><v-icon>delete</v-icon></v-btn>
							</v-list-tile-action>
						</v-list-tile>
					</div>
				</v-list>
			</v-card>
		</v-dialog>
	</v-app>
</template>

<script>
export default {
	name: 'App',
	data(){
		return {
			modalOpen: false,
			dataList: [],
			buttons: [
				{
					title: "View Saved Orders",
					click: ()=>{
						this.modalOpen = true
					}
				},
				{
					title: "Clear Order",
					click: ()=>{
						this.loadOrder({people: [],orderInfo: {deliveryTotal: 0,name: ''}})
					}
				},
			]
		}
	},
	methods: {
		reloadOrders()
		{
			let orders = window.getOrders()
			this.$set(this, "dataList", orders);
		},
		loadOrder(order)
		{
			window.loadOrder(order)
			this.modalOpen = false;
		},
		deleteOrder(storage)
		{
			window.localStorage.removeItem(storage.index);
		}
	},
	watch: {
		modalOpen(){
			this.reloadOrders();
		}
	}
}
</script>
