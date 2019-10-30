<template>
    <div>
        <b-jumbotron>
            <b-list-group>
                <h5>Crie um nome de usuário</h5>
                <b-form-input v-model="nomeUsuario" placeholder="Nome de usuário"></b-form-input>
                <b-button v-on:click="register">Cadastrar</b-button>
            </b-list-group>
        </b-jumbotron>
    </div>
</template>

<script>
import firebase from 'firebase'

export default {  
    data: function() {
        return {
            nomeUsuario: 'Nome de usuário'
        }
    },
    props: {
        userUID: String,
        userEmail: String
    },
    methods: {
        register: function() {
            let data = {
            nomeUsuario: this.nomeUsuario,
            emailUsuario: this.userEmail,
            podeCriarSala: false
            };
            firebase.firestore().collection('usuarios').doc(this.userUID).set(data);
            this.$emit('sentUsername', this.nomeUsuario, true)
        }
    }
}
</script>