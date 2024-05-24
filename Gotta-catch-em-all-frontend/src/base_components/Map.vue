<template>
    <div id="mapWrapper" :style="wrapperStyle">
        <div v-for="(row, rowIndex) in blocks" :key="'map_row_' + rowIndex" class="row">
            <div v-for="(block, blockIndex) in row" :key="'map_block_' + rowIndex + '_' + blockIndex"
                class="block-container">
                <img v-if="block == 4" class="block overlay" alt="" src="@/assets/map/high_grass_transparent.png" />
                <img class="block" alt="" :src="imgSrc(block)" />
            </div>
        </div>
    </div>
</template>

<script>
import { blocks } from '@/globals';
import { glossary, mapBlockSize } from '@/base_globals';

export default {
    name: 'Map',
    emits: ['onEnteredGrass', 'onExitedGrass', 'onStartedMoving', 'onFinishedMoving'],
    props: {
        playerX: {  // This variable keeps track of the DESIRED position X of the player in the map.
            type: Number,
            required: true
        },
        playerY: {  // This variable keeps track of the DESIRED position Y of the player in the map.
            type: Number,
            required: true
        }
    },
    data() {
        return {
            blocks: blocks,
            gameClock: 0,
            oldX: this.playerX,
            oldY: this.playerY,
            movementCounter: 0,
            moving: false
        };
    },
    computed: {
        pixelsPerFrame() {
            return mapBlockSize / 16;
        },
        pixelX() {
            let pos = this.calculatePixelPosition(this.oldX);
            if (this.oldX > this.playerX) {
                pos -= this.movementCounter * this.pixelsPerFrame;
            } else if (this.oldX < this.playerX) {
                pos += this.movementCounter * this.pixelsPerFrame;
            }
            return pos;
        },
        pixelY() {
            let pos = this.calculatePixelPosition(this.oldY);
            if (this.oldY > this.playerY) {
                pos -= this.movementCounter * this.pixelsPerFrame;
            } else if (this.oldY < this.playerY) {
                pos += this.movementCounter * this.pixelsPerFrame;
            }
            return pos;
        },
        wrapperStyle() {
            const height = this.blocks.length * mapBlockSize;
            const width = this.blocks.length > 0 ? this.blocks[0].length * mapBlockSize : 0;
            const left = `calc(50% - ${this.pixelX}px)`;
            const top = `calc(50% - ${this.pixelY}px)`;
            const style = `width: ${width}px; height: ${height}px; left: ${left}; top: ${top};`;
            return style;
        }
    },
    methods: {
        calculatePixelPosition(value) {
            return (value * mapBlockSize) + (mapBlockSize / 2);
        },
        imgSrc(spriteId) {
            return spriteId != null && spriteId in glossary ? glossary[spriteId].asset : '';
        },
        grassTrans() {
            blocks.forEach((row, rowIndex) => {
                row.forEach((block, blockIndex) => {
                    if (block == 4) {

                    }
                });
            });
        },
        manageGrassEnterAndLeave() {
            if (
                !this.blocks || this.blocks.length <= 0
                || this.playerY < 0 || this.playerY >= this.blocks.length || this.playerX < 0
                || this.oldY < 0 || this.oldY >= this.blocks.length || this.oldX < 0
            ) {
                return;
            }
            const oldBlockRow = this.blocks[this.oldY];
            const finalBlockRow = this.blocks[this.playerY];
            if (this.playerX >= finalBlockRow.length || this.oldX >= oldBlockRow.length) {
                return;
            }
            const oldBlock = oldBlockRow[this.oldX];
            const finalBlock = finalBlockRow[this.playerX];
            // This means we come from a block that is not highgrass, and we are going to a block that it is.
            if (oldBlock !== 4 && finalBlock === 4) {
                this.$emit("onEnteredGrass");
            } else if (oldBlock === 4 && finalBlock !== 4) {
                this.$emit("onExitedGrass");
            }
        }
    },
    watch: {
        gameClock() {
            if (this.moving) {
                if (this.movementCounter >= 15) {
                    this.moving = false;
                    this.movementCounter = 0;
                    this.$emit('onFinishedMoving');
                } else {
                    this.movementCounter++;
                }
            }
        },
        movementCounter(newMovementCounter, oldMovementCounter) {
            if (newMovementCounter == 0 && oldMovementCounter == 15) {
                this.oldX = this.playerX;
                this.oldY = this.playerY;
            }
        },
        playerX: {
            immediate: true,
            handler(newValue, oldValue) {
                this.$emit('onStartedMoving');
                if (!this.moving) {
                    this.moving = true;
                }
                if (oldValue != undefined && oldValue != null) {
                    this.oldX = oldValue;
                }
                this.manageGrassEnterAndLeave();
            }
        },
        playerY: {
            immediate: true,
            handler(newValue, oldValue) {
                this.$emit('onStartedMoving');
                if (!this.moving) {
                    this.moving = true;
                }
                if (oldValue != undefined && oldValue != null) {
                    this.oldY = oldValue;
                }
                this.manageGrassEnterAndLeave();
            }
        }
    },
    mounted() {
        const manageGameClock = () => {
            requestAnimationFrame(manageGameClock);
            this.gameClock++;
        };
        manageGameClock();
    }
};
</script>

<style scoped>
.block-container {
    position: relative;
    width: var(--map-block-size);
    height: var(--map-block-size);
}

#mapWrapper {
    position: absolute;
    z-index: 10;
    left: calc(50% - (var(--screen-size) / 2));
    top: calc(50% - (var(--screen-size) / 2));
    background-color: black !important;
}

.row {
    display: flex;
    height: var(--map-block-size);
}

.block {
    position: absolute;
    z-index: 20;
    width: var(--map-block-size);
}

.overlay {
    z-index: 60;
}
</style>
