<template>
    <Menu v-if="menuOpenPressed" :state="state" :selector-pos="selectorPosMenu" />
</template>

<script>
import Menu from '../base_components/Menu.vue';
export default {
    name: 'MenuMiddleware',
    props: ['menuOpen', 'menuPokedex', 'buttonMenu', 'buttonA', 'buttonB', 'inWildEncounter', 'inPokedex', 'buttonUp', 'buttonDown', 'save'],
    emits: ['update:menuOpen', 'update:inPokedex', 'update:menuPokedex'],
    components: {
        Menu
    },
    data() {
        return {
            state: 'START',
            selectorPosMenu: 0,
            menuOpenPressed: false,
        }
    },
    watch: {
        buttonMenu: function (newValue, oldValue) {
            if (newValue && this.state != 'SAVING' && !this.inWildEncounter && !this.inPokedex) {
                this.menuOpenPressed = true;
                this.$emit('update:menuOpen', true)
            }
        },
        menuPokedex(val) {
            if (val) {
                this.menuOpenPressed = true;
            }
        },
        buttonA: function (newValue, oldValue) {
            if (newValue && this.menuOpenPressed && !this.inWildEncounter && !this.inPokedex) {
                if (this.state == 'START') {
                    if (this.selectorPosMenu == 0 && this.menuOpenPressed == true) {
                        this.$emit('update:inPokedex', true)
                        this.menuOpenPressed = false
                        this.$emit('update:menuOpen', false)
                    }
                    if (this.selectorPosMenu == 1 && this.menuOpenPressed == true) {
                        this.state == 'SAVING'
                        this.$emit('update:menuOpen', false)
                        fetch('http://localhost:8081/save', {
                            method: 'POST',
                            headers: {
                                'Content-Type': 'application/json'
                            },
                            body: JSON.stringify(this.save)
                        }).then(() => {
                            console.log('SAVED!!!!!');
                            this.state = 'SAVED'
                        }).catch((error) => {
                            console.error('Error:', error);
                        });
                    }
                    if (this.selectorPosMenu == 2 && this.menuOpenPressed == true) {
                        this.menuOpenPressed = false
                        this.$emit('update:menuOpen', false)
                        this.$emit('update:menuPokedex', false)

                    }
                }
                if (this.state == 'SAVED' && this.menuOpen) {
                    this.menuOpenPressed = false
                    this.$emit('update:menuOpen', false)
                }
            }
        },
        buttonB: function (newValue, oldValue) {
            if (newValue && this.menuOpenPressed && !this.inWildEncounter && !this.inPokedex) {
                if (this.state == 'START' && this.menuOpen) {
                    if (this.selectorPosMenu == 0) {
                        this.menuOpenPressed = false
                        this.$emit('update:menuOpen', false)
                        this.$emit('update:menuPokedex', false)
                    }
                    if (this.selectorPosMenu == 1) {
                        this.menuOpenPressed = false
                        this.$emit('update:menuOpen', false)
                        this.$emit('update:menuPokedex', false)
                    }
                    if (this.selectorPosMenu == 2) {
                        this.menuOpenPressed = false
                        this.$emit('update:menuOpen', false)
                        this.$emit('update:menuPokedex', false)

                    }
                }
                if (this.state == 'SAVED') {
                    this.menuOpenPressed = false
                    this.$emit('update:menuOpen', false)
                    this.$emit('update:menuPokedex', false)
                }
            }
        },
        buttonUp: function (newValue, oldValue) {
            if (newValue && this.menuOpenPressed && !this.inWildEncounter && !this.inPokedex) {
                if (this.state === 'START') {
                    if (this.selectorPosMenu == 0) {
                        this.selectorPosMenu = 2
                    } else {
                        this.selectorPosMenu = Math.abs(this.selectorPosMenu - 1);
                    }
                    this.selectorPosMenu = this.selectorPosMenu % 3;
                    console.log(this.selectorPosMenu);
                }
            }
        },
        buttonDown: function (newValue, oldValue) {
            if (newValue && this.menuOpenPressed && !this.inWildEncounter && !this.inPokedex) {
                if (this.state === 'START') {
                    this.selectorPosMenu += 1
                    this.selectorPosMenu = this.selectorPosMenu % 3;
                    console.log(this.selectorPosMenu);
                }
            }
        },

    }
};
</script>