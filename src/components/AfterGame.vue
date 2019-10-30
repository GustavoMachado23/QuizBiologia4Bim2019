<template>
    <div>
        <h1>{{ this.mensagemAfterGame }}</h1>
        <img v-show="this.venceu == 1" src="https://i.pinimg.com/originals/f4/f7/d5/f4f7d57e991ee7fa0841e9bd05f70782.gif">
        <img v-show="this.venceu == 2" src="https://media.giphy.com/media/Zz4DU3LEtvD5m/giphy.gif">
        <img v-show="this.venceu == 3" src="https://media1.tenor.com/images/77e1e7725db612641039bc34d7348fd8/tenor.gif">
    </div>
</template>

<script>
export default {
    props: {
        mensagemAfterGame: String,
        venceu: Number
    }
}
</script>