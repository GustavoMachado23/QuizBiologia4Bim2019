<template>
    <div>
        <b-jumbotron>
            <b-list-group>
                <h5>Entre com sua conta ou <a v-on:click="clickLogin">registre-se</a></h5>
                <b-form-input v-model="email" placeholder="Digite seu email"></b-form-input>
                <b-form-input type="password" v-model="password" placeholder="Digite sua senha"></b-form-input>
                <b-button v-on:click="login" variant="primary">Entrar</b-button>
            </b-list-group>
        </b-jumbotron>
    </div>
</template>

<script>
import firebase from 'firebase';

export default {
    name: 'login',
    data() {
        return {
            email: '',
            password: ''
        }
    },
    methods: {
        login: function(e) {
            firebase.auth().signInWithEmailAndPassword(this.email, this.password)
                .then(user => {
                    if (user != null) {
                        user = firebase.auth().currentUser;
                        this.$emit('isLogged', true, user.email, user.uid);
                    }
                })
            e.preventDefault();
        },
        clickLogin: function() {
            this.$emit('clickLogin', false)
        }
    }
}
</script>

<style scoped>
.list-group {
    margin-bottom: 0px;
}

</style>
