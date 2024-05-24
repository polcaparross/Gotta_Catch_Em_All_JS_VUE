<script>
import Map from '../base_components/Map.vue';
import { blocks, isBlockWalkable } from '../globals.js';
export default {
    name: 'MapMiddleware',
    props: ['playerX', 'playerY', 'playerDirection', 'isMoving', 'moving'
        , 'buttonUp', 'buttonDown', 'buttonRight', 'buttonLeft', 'menuOpen'
        , 'buttonA', 'inWildEncounter', 'inPokedex', 'pokedex', 'pokemonIdExt', 'inGrass'],

    emits: ['update:playerDirection', 'update:inWildEncounter', 'update:playerX'
        , 'update:playerY', 'update:isMoving', 'update:moving', 'update:inWildEncounter'
        , 'update:pokemonIdExt', 'update:inGrass'],
    components: {
        Map
    },
    data() {
        return {
            onGrass: false,
            finishMoving: true,
            isUnown: false,
        }
    },
    methods: {
        makeMovement() {
            if (!this.inPokedex && !this.menuOpen && !this.inWildEncounter) {
                // NORMAL MOVEMENT:
                if (this.buttonUp) {
                    this.$emit('update:playerDirection', 'north');
                    if (isBlockWalkable[blocks[this.playerY - 1][this.playerX]]) {
                        this.$emit('update:playerY', this.playerY - 1);
                    }
                }
                if (this.buttonDown) {
                    this.$emit('update:playerDirection', 'south');
                    if (isBlockWalkable[blocks[this.playerY + 1][this.playerX]]) {
                        this.$emit('update:playerY', this.playerY + 1);
                    }

                }
                if (this.buttonRight) {
                    this.$emit('update:playerDirection', 'east');
                    if (isBlockWalkable[blocks[this.playerY][this.playerX + 1]]) {
                        this.$emit('update:playerX', this.playerX + 1);
                    }
                }
                if (this.buttonLeft) {
                    this.$emit('update:playerDirection', 'west');
                    if (isBlockWalkable[blocks[this.playerY][this.playerX - 1]]) {
                        this.$emit('update:playerX', this.playerX - 1);
                    }
                }
            }
        },
        handleEnteredGrass() {
            this.$emit('update:inGrass', true);
            fetch('http://localhost:8081/enter_grass', {
                method: 'GET',
            }).then(response => {
                console.log(response.status);
                if (response.status === 400) {
                    this.$emit('update:inWildEncounter', true);
                } else {
                    this.$emit('update:inWildEncounter', false);
                }
            }).catch((error) => {
                console.error('Error:', error);
            });
        },
        handleExitedGrass() {
            this.$emit('update:inGrass', false);
            fetch('http://localhost:8081/leave_grass', {
                method: 'GET',
            }).catch((error) => {
                console.error('Error:', error);
            });
        },
        handleMapStartMoving() {
            this.finishMoving = false;
        },
        handleMapFinishMoving() {
            this.finishMoving = true;
            this.$emit('update:isMoving', 'standing');
            if (this.buttonUp || this.buttonDown || this.buttonLeft || this.buttonRight) {
                this.$emit('update:isMoving', 'walking');
                this.makeMovement()
            }
        },
        capturingUnown() {
            let allTrue = 0
            for (var pokemon in this.pokedex) {
                if (this.pokedex[pokemon] == true) {
                    allTrue += 1
                }
            }
            console.log(this.pokedex);
            console.log('All the pokemons');
            if (allTrue == 2) {
                fetch('http://localhost:8081/my_status_code_is_unknown', {
                    method: 'GET',
                }).then(response => {
                    if (response.status === 201) {
                        this.$emit('update:pokemonIdExt', 201);
                        this.$emit('update:inWildEncounter', true);
                    }
                }).catch((error) => {
                    console.error('Error:', error);
                });
            }
        },
    },
    watch: {
        buttonA(val) {
            if (val && !this.menuOpen && !this.inPokedex && !this.inWildEncounter) {
                if (blocks[this.playerY + 1][this.playerX] == 10 && this.playerDirection == 'south')
                    this.capturingUnown()
                if (blocks[this.playerY - 1][this.playerX] == 10 && this.playerDirection == 'north')
                    this.capturingUnown()
                if (blocks[this.playerY][this.playerX + 1] == 10 && this.playerDirection == 'east')
                    this.capturingUnown()
                if (blocks[this.playerY][this.playerX - 1] == 10 && this.playerDirection == 'west')
                    this.capturingUnown()
            }
        },
        buttonUp: function (newValue, oldValue) {
            if (newValue && !this.menuOpen && this.finishMoving && !this.inWildEncounter && !this.inPokedex) {
                this.$emit('update:isMoving', 'walking');
                this.makeMovement()
            }
        },
        buttonDown: function (newValue, oldValue) {
            if (newValue && !this.menuOpen && this.finishMoving && !this.inWildEncounter && !this.inPokedex) {
                this.$emit('update:isMoving', 'walking');
                this.makeMovement()
            }
        },
        buttonLeft: function (newValue, oldValue) {
            if (newValue && !this.menuOpen && this.finishMoving && !this.inWildEncounter && !this.inPokedex) {
                this.$emit('update:isMoving', 'walking');
                this.makeMovement()
            }
        },
        buttonRight: function (newValue, oldValue) {
            if (newValue && !this.menuOpen && this.finishMoving && !this.inWildEncounter && !this.inPokedex) {
                this.$emit('update:isMoving', 'walking');
                this.makeMovement()
            }
        }
    }
}
</script>

<template>
    <Map :player-x="playerX" :player-y="playerY" @on-entered-grass="handleEnteredGrass"
        @on-exited-grass="handleExitedGrass" @on-started-moving="handleMapStartMoving"
        @on-finished-moving="handleMapFinishMoving" />
</template>