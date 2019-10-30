<template>
    <div>
        <h6>Digite o código da sala que você deseja entrar</h6>
        <b-form-input v-model="codSala" placeholder="Código da Sala"></b-form-input>
        <b-button v-on:click="entrarSala">Entrar</b-button>
    </div>
</template>

<script>
import firebase from 'firebase'

export default {
    data: function() {
        return {
            codSala: '',
            waitListFirebase: [],
            p1ToUpload: 'NoOne',
            p2ToUpload: 'NoOne',
            whichPlayer: 0,
        };
    },
    props: {
        userUID: String
    },
    methods: {
        entrarSala: function() {
            firebase.firestore().collection('partidas').doc(this.codSala).get().then((snapshot) => {
                if (snapshot.data() !== undefined) {
                    console.log(snapshot.data().waitList.length)
                    this.waitListFirebase = snapshot.data().waitList
                    if(this.waitListFirebase[1] == 'NoOne'){
                        this.whichPlayer = 1
                        this.waitListFirebase[1] = this.userUID
                        this.p1ToUpload = this.waitListFirebase[1]
                        let data = {
                            waitList: this.waitListFirebase
                        }
                        firebase.firestore().collection('partidas').doc(this.codSala).update(data)
                    } else if(this.waitListFirebase[2] == 'NoOne'){
                        this.whichPlayer = 2
                        this.waitListFirebase[2] = this.userUID
                        this.p2ToUpload = this.waitListFirebase[2]
                        let data = {
                            waitList: this.waitListFirebase
                        }
                        firebase.firestore().collection('partidas').doc(this.codSala).update(data)
                    }
                    this.$emit('aprovado', this.codSala, this.whichPlayer)
                }
            })
        }
    }
}
</script>
