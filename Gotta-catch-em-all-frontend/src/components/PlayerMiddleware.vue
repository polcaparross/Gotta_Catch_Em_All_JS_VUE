<template>
    <Player :facing="playerDirection" :movement-type="isMoving" :in-grass="inGrass" />
</template>

<script>
import Player from '../base_components/Player.vue';

export default {
    name: 'PlayerMiddleware',
    components: {
        Player
    },
    props: ['playerDirection', 'isMoving', 'inGrass'],
    emits: [],
    data() {
        return {
        }
    }
};
</script>