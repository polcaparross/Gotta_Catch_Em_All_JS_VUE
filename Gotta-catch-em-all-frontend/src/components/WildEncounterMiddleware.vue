<script>
import WildEncounter from "../base_components/WildEncounter.vue";
export default {
    name: "App",
    components: {
        WildEncounter,
    },
    props: ["pokedex", "buttonA", "buttonB", 'buttonUp'
        , 'buttonDown', "inWildEncounter", 'pokemonIdExt'],
    emits: ["update:pokedex", 'update:inWildEncounter', 'update:buttonA'],
    data() {
        return {
            state: "START",
            selectorPosWild: 0,
            pokemonId: 0,
            isPokemonOwned: false,

        };
    },
    methods: {
        calculatePokemonId() {
            const randomNum = Math.floor(Math.random() * 10);
            if (this.pokemonIdExt != 201) {
                this.pokemonId = randomNum < 5 ? 16 : 19;
            } else {
                this.pokemonId = 201;
            }
            const isRegistered = this.pokedex[this.pokemonId] != undefined
            if (!isRegistered) {
                this.pokedex[this.pokemonId] = false
                console.log(this.pokedex);
                this.$emit("update:pokedex", this.pokedex);
            }
        },
        captureSuccess() {
            fetch('http://localhost:8081/capture', {
                method: 'GET',
            }).then(response => {
                if (response.status == 200) {
                    this.state = "CAUGHT"
                    this.isPokemonOwned = true
                    this.pokedex[this.pokemonId] = true
                    this.$emit("update:pokedex", this.pokedex);
                } else if (response.status == 400) {
                    this.state = "MAIN"
                }
            })
        }

    },
    mounted() {
        this.calculatePokemonId();
    },
    watch: {
        state(newValue) {
            if (newValue === "CATCHING") {
                this.captureSuccess()
            }
        },
        buttonA(newValue) {
            if (this.inWildEncounter && newValue == true) {
                if (this.state === "START") {
                    setTimeout(() => {
                        this.state = "MAIN";
                    }, 200);
                }
                if (this.state === "MAIN") {
                    this.$emit("update:inWildEncounter", true);
                    if (this.selectorPosWild == 0) {
                        this.state = "CATCHING";
                    }

                    if (this.selectorPosWild == 1 && this.state !== "RUNNING_AWAY") {
                        setTimeout(() => {
                            this.state = "RUNNING_AWAY";
                        }, 200);
                    }
                }
                if (this.state == "RUNNING_AWAY") {
                    this.$emit("update:inWildEncounter", false);
                    this.state = "START"
                }

                if (this.state == "CAUGHT") {
                    this.$emit("update:inWildEncounter", false);
                    this.state = "START"
                }
            }
        },
        buttonB(newValue) {
            if (this.inWildEncounter && newValue == true) {
                if (this.state === "START") {
                    setTimeout(() => {
                        this.state = "MAIN";
                    }, 200);
                }
                if (this.state === "CAUGHT") {
                    this.$emit("update:inWildEncounter", false);
                    this.state = "START"
                }
                if (this.state === "RUNNING_AWAY") {
                    this.$emit("update:inWildEncounter", false);
                    this.state = "START"
                }
            }
        },
        buttonUp() {
            if (this.inWildEncounter) {
                if (this.state === "MAIN") {
                    if (this.buttonUp) {
                        this.selectorPosWild = this.selectorPosWild == 0 ? 1 : 0;
                    }
                }
            }
        },
        buttonDown() {
            if (this.inWildEncounter) {
                if (this.state === "MAIN") {
                    if (this.buttonDown) {
                        this.selectorPosWild = this.selectorPosWild == 0 ? 1 : 0;
                    }
                }
            }
        },
    }
}
</script>

<template>
    <WildEncounter v-if="inWildEncounter" :state="state" :selector-pos="selectorPosWild" :pokemon-id="pokemonId"
        :is-pokemon-owned="isPokemonOwned" />
</template>
