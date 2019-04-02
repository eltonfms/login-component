<template>
    <div class="hello">
        <login-component
            passwordPattern=".{4,10}"
            passwordMessage="Deve conter entre 4 e 10 caracteres."
            :errorMessage="credentialFail"
            @loginCredentials="loginAttempt"
        ></login-component>
    </div>
</template>

<script>
import LoginComponent from './LoginComponent.vue'
    
export default {
    components: {LoginComponent},
    name: 'HelloWorld',

    data () {
        return {
            credentialFail: ''
        }
    },

    methods: {
        loginAttempt: function (credentials) {
            console.log(credentials.email);
            console.log(credentials.password);

            this.credentialFail = '';

            setTimeout( () => {
                this.credentialFail = 'E-mail ou senha inválidos.'
            }, 2000)
        }
    }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

</style>
