<template>
    <div>
        <h6>Digite o nome da sala que você deseja criar</h6>
        <b-form-input v-model="nomeSala" placeholder="Nome da Sala"></b-form-input>
        <b-button v-on:click="criarSala">Criar</b-button>
    </div>
</template>

<script>
import firebase from 'firebase'

export default {
    data: function() {
        return {
            nomeSala: ''
        };
    },
    methods: {
        criarSala: function() {
            var salaID = Math.floor(Math.random() * 100000)
            firebase.firestore().collection('partidas').doc('S' + salaID).get().then((snapshot) => {
                if (snapshot.data() == undefined) {
                    let data = {
                        nomeSala: this.nomeSala,
                        waitList: ['', 'NoOne', 'NoOne'],
                        p1: '',
                        p2: '',
                        p1At: 0,
                        p2At: 0,
                        p1Acertos: 0,
                        p2Acertos: 0,
                        comecar: false,
                        limite: 20,
                        vencedor: 0
                    };
                    firebase.firestore().collection('partidas').doc('S' + salaID).set(data)
                    this.$emit('IdSala', 'S' + salaID)
                }
            })
        }
    }
}
</script>