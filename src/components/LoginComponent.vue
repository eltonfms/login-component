<template>
	<div class="mdl-grid">
		<div class="mdl-layout-spacer"></div>

		<div class="mdl-card mdl-shadow--2dp login-card-size">

			<form @submit.prevent="logMeIn">

				<div class="mdl-card__title mdl-color--primary">
					<h2 class="mdl-card__title-text mdl-color-text--white">Login</h2>
				</div>

				<div class="mdl-card__supporting-text">

					<div class="mdl-textfield mdl-js-textfield mdl-textfield--floating-label">
						<label for="email" class="mdl-textfield__label">Endereço de E-mail:</label>
						<input type="email" ref="txtEmail" @input="checkEmailValidator" class="mdl-textfield__input" name="email" id="email" autocomplete="off">
						<span class="mdl-textfield__error">Digite um e-mail válido.</span>
					</div>

					<div class="mdl-textfield mdl-js-textfield mdl-textfield--floating-label">
						<label for="password" class="mdl-textfield__label">Senha:</label>
						<input type="password" ref="txtPassword" @input="checkPasswordValidator" class="mdl-textfield__input" name="password" id="password" :pattern="passwordPattern" autocomplete="off">
						<span class="mdl-textfield__error">{{ passwordMessage }}</span>
					</div>

					<label class="mdl-checkbox mdl-js-checkbox mdl-js-ripple-effect">
						<input type="checkbox" v-model="rememberMe" class="mdl-checkbox__input"> Lembrar de mim
					</label>

				</div>

				<div class="mdl-card__actions mdl-card--border">
					<button class="mdl-button mdl-js-button mdl-button--raised mdl-js-ripple-effect mdl-button--accent" :disabled="submitBtnDisabled">
						<span v-if="isLoading">Acessando...</span>
						<span v-else>Fazer login</span>
					</button>
					<span v-if="errorMessage" class="mdl-color-text--red-500">{{ errorMessage }}</span>
				</div>

			</form>

		</div>

		<div class="mdl-layout-spacer"></div>
	</div>
</template>

<script>
export default {
	name: 'LoginComponent',

	props: {
		passwordMessage: {
			type: String,
			required: false,
			default: 'Deve conter entre 4 e 10 caracteres.',
		},
		passwordPattern: {
			type: String,
			required: true,
			default: '.{4,10}',
			validator: function(value) {
				return value !== '';
			},
		},
		errorMessage: {
			type: String,
			required: false,
			default: '',
		}
	},

	data () {
		return {
			submitBtnDisabled: true,
			isLoading: false,
			rememberMe: false,
		}
	},

	watch: {
		errorMessage: function () {
			if(this.errorMessage !== ''){
				this.isLoading = false;
				this.submitBtnDisabled = false;
			}
		}
	},

	mounted: function () {
		let email = document.cookie.match('(^|;)\\s*' + 'email' + '\\s*=\\s*([^;]+)')
		let password = document.cookie.match('(^|;)\\s*' + 'password' + '\\s*=\\s*([^;]+)')
		this.$refs.txtEmail.value = email ? email.pop() : ''
		this.$refs.txtPassword.value = password ? password.pop() : ''
		if (email) this.submitBtnDisabled = false
		console.log('Verificando cookies: ' + document.cookie)
	},

	methods: {
		logMeIn: function () {
			let email = this.$refs.txtEmail.value.trim();
			let password = this.$refs.txtPassword.value.trim();

			// COOKIE FUNCTIONS!
			// 'key=value; expires=current dateTime in UTC; path=/'
			if (this.rememberMe) {
				let d = new Date()
				d.setTime(d.getTime() + (180 * 24 * 60 * 60 * 1000)) //
				document.cookie = 'email=' + email + ';expires=' + d.toUTCString() + ';path=/'
				document.cookie = 'password=' + password + ';expires=' + d.toUTCString() + ';path=/'
				console.log('Cookies setados: ' + document.cookie)
			} else {
				document.cookie = 'email=; expires=Thu, 01 Jan 1970 00:00:00 UTC; path=/;'
				document.cookie = 'password=; expires=Thu, 01 Jan 1970 00:00:00 UTC; path=/;'
				console.log('Cookies deletados: ' + document.cookie)
			}

			// INDICATE WORKING & CLEAN UP ANY PREVIOUS ERRORS
			this.isLoading = true;
			this.submitBtnDisabled = true;

			this.$emit('loginCredentials', {
				'email': email,
				'password': password,
			});
		},
		checkEmailValidator: function () {
			if (this.$refs.txtEmail.checkValidity() && this.$refs.txtEmail.value !== '') {
				if (this.$refs.txtPassword.checkValidity() && this.$refs.txtPassword.value !== '') {
					this.submitBtnDisabled = false;
				} else {
					this.submitBtnDisabled = true;
				}
			}
		},
		checkPasswordValidator: function () {
			if (this.$refs.txtPassword.checkValidity() && this.$refs.txtPassword.value !== '') {
				if (this.$refs.txtEmail.checkValidity() && this.$refs.txtEmail.value !== '') {
					this.submitBtnDisabled = false;
				} else {
					this.submitBtnDisabled = true;
				}
			}
		}
	}
}

</script>

<style scoped>
	.login-card-size{
		max-width: 320px;
		height: auto;
	}
</style>