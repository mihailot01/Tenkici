<template>
    <div id="tenkici">
        <h1 style="padding: 0.5rem;margin-bottom: 0;">Tenkići</h1>
        <Game />
    </div>
</template>


<script>

import Game from '@/components/Game.vue'

export default {
    name: 'Tenkici',
    components:{
        Game
    },
    data: function(){
        return {
            test: "test"

        }
    }


}
</script>



<style>
    body{
        background-color: gainsboro;
    }
    #tenkici{
        background-color: whitesmoke;
        max-width: 1200px;
        margin: auto;
    }
</style>