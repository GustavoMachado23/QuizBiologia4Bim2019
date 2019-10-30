<template>
    <div>
        <b-jumbotron>
            <b-list-group>
                <h5>Registre-se ou <a v-on:click="clickLogin">entre com sua conta</a></h5>
                <b-form-input v-model="email" placeholder="Digite seu email"></b-form-input>
                <b-form-input type="password" v-model="password" placeholder="Crie uma senha"></b-form-input>
                <b-button v-on:click="register" variant="success">Registrar-se</b-button>
            </b-list-group>
        </b-jumbotron>
    </div>
</template>

<script>
import firebase from 'firebase';

export default {
    name: 'register',
    data: function() {
        return {
            email: '',
            password: ''
        };
    },
    methods: {
        register: function(e) {
            firebase.auth().createUserWithEmailAndPassword(this.email, this.password)
                .then(user => {
                    if (user != null) {
                        user = firebase.auth().currentUser;
                        this.$emit('isLogged', true, user.email, user.uid);
                    }
                })
            e.preventDefault();
        },
        clickLogin: function() {
            this.$emit('clickLogin', true)
        }
    }
}
</script>

<style scoped>
.list-group {
    margin-bottom: 0px;
}

</style>
