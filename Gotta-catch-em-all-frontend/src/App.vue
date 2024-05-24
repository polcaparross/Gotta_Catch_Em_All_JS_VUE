<script>
import ControlsFrame from './base_components/ControlsFrame.vue';
import MapMiddleware from './components/MapMiddleware.vue';
import PlayerMiddleware from './components/PlayerMiddleware.vue';
import WildEncounterMiddleware from './components/WildEncounterMiddleware.vue'
import MenuMiddleware from './components/MenuMiddleware.vue'
import PokedexMiddleware from './components/PokedexMiddleware.vue'
import { blocks, isBlockWalkable } from './globals';

export default {
  name: 'App',
  components: {
    ControlsFrame,
    MapMiddleware,
    PlayerMiddleware,
    WildEncounterMiddleware,
    MenuMiddleware,
    PokedexMiddleware,
  },
  data() {
    return {
      save: {
        x: 12,
        y: 6,
        direction: 'south',
        pokedex: {}
      },
      isMoving: 'standing',
      menuOpen: false,
      inWildEncounter: false,
      inPokedex: false,
      buttonUp: false,
      buttonDown: false,
      buttonRight: false,
      buttonLeft: false,
      buttonMenu: false,
      buttonA: false,
      buttonB: false,
      pokemonIdExt: 0,
      menuPokedex: false,
      inGrass: false,
    }
  },
  mounted() {
    fetch('http://localhost:8081/initial_info', {
      method: 'GET',
    }).then(response => response.json())
      .then(data => {
        if (this.save) {
          console.log('Initial info:', data);
          console.log(this.save);
          this.save.x = data.x;
          this.save.y = data.y;
          this.save.direction = data.direction;
          this.save.pokedex = data.pokedex;
        }
      })
      .catch((error) => {
        console.error('Error:', error);
      });

  },

  methods: {
    handleMovementUp(pressed) {
      console.log('Movement up:', pressed);
      this.buttonUp = pressed
    },
    handleMovementDown(pressed) {
      console.log('Movement down:', pressed);
      this.buttonDown = pressed
    },
    handleMovementLeft(pressed) {
      console.log('Movement left:', pressed);
      this.buttonLeft = pressed
    },
    handleMovementRight(pressed) {
      console.log('Movement right:', pressed);
      this.buttonRight = pressed
    },
    handleButtonA(pressed) {
      console.log('Button A:', pressed);
      this.buttonA = pressed
    },
    handleButtonB(pressed) {
      console.log('Button B:', pressed);
      this.buttonB = pressed
    },
    handleButtonMenu(pressed) {
      console.log('Button Menu:', pressed);
      this.buttonMenu = pressed
    },

  }
};
</script>

<template>
  <div id="app">
    <ControlsFrame @onMovementUp="handleMovementUp" @onMovementDown="handleMovementDown"
      @onMovementLeft="handleMovementLeft" @onMovementRight="handleMovementRight" @onButtonA="handleButtonA"
      @onButtonB="handleButtonB" @onButtonMenu="handleButtonMenu" />

    <MapMiddleware v-model:in-wild-encounter="inWildEncounter" v-model:player-x="save.x" v-model:player-y="save.y"
      v-model:player-direction="save.direction" v-model:is-moving="isMoving" :button-up="buttonUp"
      :button-down="buttonDown" :button-a="buttonA" :pokedex="save.pokedex" :button-right="buttonRight"
      :button-left="buttonLeft" :menu-open="menuOpen" v-model:pokemon-id-ext="pokemonIdExt" :in-pokedex="inPokedex"
      v-model:in-grass="inGrass" />

    <PlayerMiddleware :player-direction="save.direction" :is-moving="isMoving" :in-grass="inGrass" />

    <WildEncounterMiddleware v-if="inWildEncounter" v-model:pokedex="save.pokedex" v-model:button-a="buttonA"
      :button-b="buttonB" :button-up="buttonUp" :button-down="buttonDown" v-model:in-wild-encounter="inWildEncounter"
      :pokemon-id-ext="pokemonIdExt" />

    <MenuMiddleware v-model:menu-open="menuOpen" v-model:in-pokedex="inPokedex" :button-menu="buttonMenu"
      :in-wild-encounter="inWildEncounter" :button-a="buttonA" :button-b="buttonB" :button-up="buttonUp"
      :button-down="buttonDown" :save="save" v-model:menu-pokedex="menuPokedex" />

    <PokedexMiddleware v-model:in-pokedex="inPokedex" :button-a="buttonA" :button-b="buttonB" :pokedex="save.pokedex"
      v-model:menu-open="menuOpen" v-model:menu-pokedex="menuPokedex" />
  </div>
</template>
