<template>
	<div id="app" class="container">
		<h1>HTTP com Axios</h1>
		<b-card v-if="!getUsersClicked">
			<!-- <b-alert show variant="success" v-if="success">Sucesso</b-alert> -->
			<b-form-group 
				label="Enter your name">
					<b-form-input :type="'text'" placeholder="Enter your name" v-model="user.name"></b-form-input>
			</b-form-group>
			<b-form-group 
				description="Tell us about ya ☺"
				label="Enter your e-mail">
				<b-form-input :type="'email'" placeholder="Enter your e-mail" v-model="user.email"></b-form-input>
			</b-form-group>
			<hr>
			<b-button variant="outline-primary" size="lg" @click="save">Save</b-button>
			<b-button variant="primary" class="ml-2" size="lg" @click="getUsers">Get Users</b-button>
		</b-card>
		<b-card v-if="getUsersClicked">
			<b-button variant="primary" size="sm" class="mb-4" @click="getUsersClicked = !getUsersClicked">Return</b-button>
			<b-list-group>
				<b-list-group-item v-for="(user, ix) in users" :key="ix">
					Name: <strong>{{ user.name }}</strong><br>
					E-mail: <strong>{{ user.email }}</strong><br>
					<b-button variant="secondary" size="sm" @click="load(ix)">Load</b-button>
					<b-button variant="warning" class="ml-2" size="sm" @click="deleteUser(ix)">Delete</b-button>
				</b-list-group-item>
			</b-list-group>
		</b-card>
	</div>
</template>

<script>
export default {
	data(){
		return {
			user: {
				name: '',
				email: '',
			},
			ix: null,
			users: [],
			getUsersClicked: false,
			success: false,
		}
	},
	methods: {
		clean(){
			this.user.name = ''
			this.user.email  = ''
			this.ix  = null
		},
		load(ix){
			this.getUsersClicked =  false
			this.user = { ...this.users[ix]}
			this.ix = ix
		},
		deleteUser(ix){
			// console.log(`delete ix ${ix}`)
			this.$http.delete(`/users/${ix}.json`)
				.then(() => this.getUsers())

			this.messageAlert('sucessOnDelete')
		},
		save(){
			// console.log(`Username: ${this.user.name} | usermail: ${this.user.email}`)
			const method = this.ix ? 'patch' : 'post'
			const finalUrl = this.ix ? `/${this.ix}.json` : '.json'
			// console.log(`ix:${this.ix} | method:${method} | finalUrl:${finalUrl}`)
			this.$http[method](`/users${finalUrl}` , this.user).then(() => this.clean())
			
			method == 'post' ? this.messageAlert('sucessOnSave') : this.messageAlert('sucessOnChange')
		},
		getUsers(){
			this.getUsersClicked = true
			// this.$http.get('users.json').then(res =>{
			this.$http('users.json').then(res =>{
				this.users = res.data
			})
		},
		messageAlert(type) {
			switch(type) {
				case 'sucessOnSave':
					alert('Usuário inserido com sucesso!')
					break;
				case 'sucessOnDelete':
					alert('Usuário deletado com sucesso!')
					break;
				case 'sucessOnChange':
					alert('Usuário alterado com sucesso!')
					break;
			} 
		}
	}
	// created(){
	// 	this.$http.post('users.json', {
	// 		name: 'Ekatherina Vladmirovichna',
	// 		email: 'eka@yandex.com.ru'
	// 	}).then(res => console.log(res))
	// }

}
</script>

<style>
#app {
	font-family: 'Avenir', Helvetica, Arial, sans-serif;
	-webkit-font-smoothing: antialiased;
	-moz-osx-font-smoothing: grayscale;
	color: #2c3e50;
	font-size: 1.5rem;
}

#app h1 {
	text-align: center;
	margin: 50px;
}

body {
	background: linear-gradient(90deg, rgba(2,0,36,1) 0%, rgba(58,9,121,0.28532909745929624) 35%, rgba(0,212,255,1) 100%);

}
</style>
