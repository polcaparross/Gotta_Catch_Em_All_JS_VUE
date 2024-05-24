<script>
import Pokedex from '../base_components/Pokedex.vue';
import { pokemonNames } from '../globals.js';
export default {
    name: 'App',
    props: ["inPokedex", "buttonA", "buttonB", "pokedex", 'menuOpen', 'menuPokedex'],
    emits: ["update:inPokedex", "update:menuOpen", 'update:menuPokedex'],
    components: {
        Pokedex
    },
    data() {
        return {
            numberOfOwnedPokemons: 0,
            numberOfSeenPokemons: 0,
            pokemonsInfo: [],
        }
    },
    methods: {
        calculateSeen() {
            this.numberOfSeenPokemons = 0
            for (var pokemon in this.pokedex) {
                if (this.pokedex[pokemon] == false || this.pokedex[pokemon] == true)
                    this.numberOfSeenPokemons += 1
            }
        },
        calculateOwned() {
            this.numberOfOwnedPokemons = 0
            for (var pokemon in this.pokedex) {
                if (this.pokedex[pokemon] == true)
                    this.numberOfOwnedPokemons += 1
            }
        },
        makePokemonInfo() {
            this.pokemonsInfo = []
            for (var pokemon in this.pokedex) {
                let pokemonName = pokemonNames[pokemon].name
                this.pokemonsInfo.push({
                    "id": pokemon,
                    "owned": this.pokedex[pokemon],
                    "name": pokemonName,
                })
            }
        },
    },
    watch: {
        inPokedex(val) {
            if (val) {
                this.calculateSeen()
                this.calculateOwned()
                this.makePokemonInfo()
                console.log(this.pokedex);
            }
        },
        buttonB() {
            if (this.inPokedex) {
                this.$emit('update:inPokedex', false)
                this.$emit('update:menuOpen', true)
                this.$emit('update:menuPokedex', true)
            }
        }
    }
};
</script>

<template>
    <Pokedex v-if="inPokedex" :number-of-owned-pokemons="numberOfOwnedPokemons"
        :number-of-seen-pokemons="numberOfSeenPokemons" :pokemons-info="pokemonsInfo" />
</template>