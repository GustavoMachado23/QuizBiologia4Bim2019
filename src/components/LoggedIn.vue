<template>
    <div>
        <b-jumbotron>
            <b-list-group>
                <h5>Você está logado com {{ defUsername }}</h5>
                <div v-show="logToSala">
                    <RoomCode 
                        v-show="!logCriarSala"
                        :userUID="userUID"
                        @aprovado="aprovado"
                    />
                    <CreateRoom 
                        v-show="logCriarSala"
                        @IdSala="IdSala"
                    />
                </div>
                <div v-show="!logToSala">
                    <AdminRoom
                        v-show="logCriarSala"
                        :IdSalaCriada="IdSalaCriada"
                        :logToSala="logToSala"
                        :logCriarSala="logCriarSala"
                    />
                    <SalaDeEspera 
                        v-show="!logCriarSala"
                        @updateGame="updateGame"
                    />
                </div>
            </b-list-group>
        </b-jumbotron>
    </div>
</template>

<script>
import CreateRoom from './LoggedInCreateRoom'
import RoomCode from './LoggedInTypeRoomCode'
import AdminRoom from './CreatedRoom'
import SalaDeEspera from './SalaDeEspera'

export default {
    data: function() {
        return {
            IdSalaCriada: 'NenhumaSalaAinda',
            logToSala: true,
            player1: 'NoOne',
            player2: 'NoOne'
        }
    },
    props: {
        defUsername: String,
        logCriarSala: Boolean,
        userUID: String
    },
    components: {
        CreateRoom,
        RoomCode,
        AdminRoom,
        SalaDeEspera
    },
    methods: {
        IdSala(idS) {
            this.IdSalaCriada = idS
            this.logToSala = false
        },
        aprovado(idSala, whichPlayer) {
            this.IdSalaCriada = idSala
            this.logToSala = false
            this.$emit('IdSalaCriadaMethod', this.IdSalaCriada, whichPlayer)
        },
        updateGame() {
            this.$emit('updateGameT')
        }
    }
}
</script>