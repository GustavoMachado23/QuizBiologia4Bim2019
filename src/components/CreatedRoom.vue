<template>
    <div>
        <h1>{{ this.IdSalaCriada }}</h1>
        <h1>{{ this.fraseFinalPartida + this.vencedorPartida }}</h1>
        <b-card-group deck>
            <b-card bg-variant="primary" text-variant="white" class="text-center">
                <h3>{{ this.p1nome }}</h3>
                <h1>{{ this.p1Acertos }} / {{ this.p1At }}</h1>
            </b-card>
            <b-card bg-variant="danger" text-variant="white" class="text-center">
                <h3>{{ this.p2nome }}</h3>
                <h1>{{ this.p2Acertos }} / {{ this.p2At }}</h1>
            </b-card>
        </b-card-group>
        <b-button v-on:click="updateInfo">Atualizar</b-button>
    </div>
</template>

<script>
import firebase from 'firebase'

export default {
    data: function() {
        return {
            p1: '',
            p2: '',
            p1nome: '',
            p2nome: '',
            p1At: 0,
            p2At: 0,
            p1Acertos: 0,
            p2Acertos: 0,
            p1UIDForSC: 'NoOne',
            p2UIDForSC: 'NoOne',
            fraseFinalPartida: '',
            vencedorPartida: ''
        }
    },
    props: {
        IdSalaCriada: String,
        logToSala: Boolean,
        logCriarSala: Boolean
    },
    methods: {
        updateInfo() {
            if(this.logToSala == false && this.logCriarSala == true){
                firebase.firestore().collection('partidas').doc(this.IdSalaCriada).onSnapshot(res => {
                    console.log(res.data().waitList)
                    this.p1At = res.data().p1At
                    this.p2At = res.data().p2At
                    this.p1Acertos = res.data().p1Acertos
                    this.p2Acertos = res.data().p2Acertos
                    this.p1UIDForSC = res.data().p1
                    this.p2UIDForSC = res.data().p2
                    let data = {
                        p1: res.data().waitList[1],
                        p2: res.data().waitList[2]
                    }
                    firebase.firestore().collection('partidas').doc(this.IdSalaCriada).update(data)
                    firebase.firestore().collection('usuarios').doc(res.data().waitList[1]).get().then((snap1) => {
                        this.p1nome = snap1.data().nomeUsuario
                        console.log(this.p1nome)
                    })
                    firebase.firestore().collection('usuarios').doc(res.data().waitList[2]).get().then((snap2) => {
                        this.p2nome = snap2.data().nomeUsuario
                        console.log(this.p2nome)
                    })
                    if(this.p1UIDForSC !== 'NoOne'){
                        if(this.p2UIDForSC !== 'NoOne'){
                            let dataComecar = {
                                comecar: true
                            }
                            firebase.firestore().collection('partidas').doc(this.IdSalaCriada).update(dataComecar)
                            console.log('Começando')
                        }
                    }
                    if(res.data().p1At >= res.data().limite && res.data().p2At >= res.data().limite){
                        if(res.data().p1Acertos > res.data().p2Acertos){
                            console.log('Parabéns Player1')
                            this.fraseFinalPartida = "A partida acabou e o vencedor é "
                            this.vencedorPartida = this.p1nome
                            let dataFinal = {
                                vencedor: 1
                            }
                            firebase.firestore().collection('partidas').doc(this.IdSalaCriada).update(dataFinal)
                        } else if(res.data().p1Acertos < res.data().p2Acertos){
                            console.log('Parabéns Player2')
                            this.fraseFinalPartida = "A partida acabou e o vencedor é "
                            this.vencedorPartida = this.p2nome
                            let dataFinal = {
                                vencedor: 2
                            }
                            firebase.firestore().collection('partidas').doc(this.IdSalaCriada).update(dataFinal)
                        } else if(res.data().p1Acertos == res.data().p2Acertos){
                            console.log('Houve um empate!')
                            this.fraseFinalPartida = "A partida acabou e houve um empate!"
                            let dataFinal = {
                                vencedor: 3
                            }
                            firebase.firestore().collection('partidas').doc(this.IdSalaCriada).update(dataFinal)
                        }
                    }
                })
            }
        }
    },
    created() {
        this.updateInfo()
    }
}
</script>
