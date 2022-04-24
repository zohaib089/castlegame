<template>
    <div class="land-list game-scroll-bar row" v-if="lands.length !== 0">
        <div
            class="col"
            :class="gridCol"
            v-for="land in lands"
            :key="land.id"
        >
            <land-item
                :land="land"
                :action="action"
            />
        </div>
    </div>

    <div class="land-list--empty" v-if="lands.length === 0">
        <p>No lands available in this category.</p>
    </div>
</template>

<script>
import LandItem from './LandItem.vue'

export default {
    components: {
        LandItem
    },

    props: [
        'lands',
        'action'
    ],

    data() {
        return {
            windowWidth: window.innerWidth,
            gridCol: 'c-12 l-3'
        }
    },

    watch: {
        windowWidth(newWidth) {
            if (newWidth < 530) {
                this.gridCol = 'c-12'
            } else if (newWidth < 740) {
                this.gridCol = 'c-6'
            } else if (newWidth < 1300) {
                this.gridCol = 'c-12 l-6'
            } else if (newWidth < 1600) {
                this.gridCol = 'l-4'
            } else {
                this.gridCol = 'l-3'
            }
        }
    },

    mounted() {
        if (this.windowWidth < 530) {
            this.gridCol = 'c-12 l-3'
        } else if (this.windowWidth < 740) {
            this.gridCol = 'c-6'
        } else if (this.windowWidth < 1300) {
            this.gridCol = 'c-12 l-6'
        } else if (this.windowWidth < 1600) {
            this.gridCol = 'l-4'
        }

        this.$nextTick(() => {
            window.addEventListener('resize', this.onResize);
        })
    },

    methods: {
        onResize() {
            this.windowWidth = window.innerWidth
        }
    }
}
</script>

<style scoped>
.land-list {
    flex: 1;
    overflow-y: auto;
    position: relative;
    z-index: 1;
    margin-top: 20px;
}

.land-list--empty p {
    margin-top: 16px;
    font-size: 14px;
    text-align: center;
    color: #3e1f05;
}
</style>