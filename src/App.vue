<template>
    <div>
        <div
            ref="castleOverlay"
            class="castle-overlay overlay"
            @wheel="(e) => zoomCastle(e)"
        >
            <div
                ref="castle"
                class="castle-bg"
                :style="{
                    'transform': `translate3d(${translate.x}, ${translate.y}, 0px) scale(${scale})`,
                }"
            >
                <!-- fire -->
                <div class="fire-wrap fire-wrap-1">
                    <div class="fire"></div>
                </div>
                <div class="fire-wrap fire-wrap-2">
                    <div class="fire"></div>
                </div>
                <div class="fire-wrap fire-wrap-3">
                    <div class="fire"></div>
                </div>

                <!-- hall light -->
                <div class="hall-light-wrap hall-light-wrap-1">
                    <div class="hall-light"></div>
                </div>

                <div class="hall-light-wrap hall-light-wrap-2">
                    <div class="hall-light"></div>
                </div>

                <div class="hall-light-wrap hall-light-wrap-3">
                    <div class="hall-light"></div>
                </div>

                <!-- tourch -->
                <div class="torch-wrap torch-wrap-1">
                    <div class="torch"></div>
                </div>
                <div class="torch-wrap torch-wrap-2">
                    <div class="torch"></div>
                </div>

                <!-- beast -->
                <div class="beast-wrap-1">
                    <div class="beast-1"></div>
                </div>

                <div class="beast-wrap-2">
                    <div class="beast-2"></div>
                </div>

                <!-- throne light -->
                <div class="throne-light-wrap">
                    <div class="throne-light"></div>
                </div>

                <!-- Jester Grandle -->
                <div class="jester-container">
                    <div class="jester-wrap">
                        <div class="jester"></div>
                    </div>

                    <div
                        class="jester-grandle-btn click-cursor"
                        @click="this.$refs.jesterModal.toggleModal()"
                    >
                        <game-button title="Jester Grandle" />
                    </div>
                </div>

                <!-- Land Auctions -->
                <div
                    class="land-auction-btn click-cursor"
                    @click="this.$refs.landAuctionModal.toggleModal()"
                >
                    <game-button title="Land Auction"/>
                </div>
            </div>
        </div>

        <modal
            ref="landAuctionModal"
            fancyTitle="Land Auction"
            height="316"
            width="600"
        >
            <button
                class="green-button click-cursor"
                @click="() => {
                    this.$refs.landAuction.toggle();
                    this.action = 0
                }"
            >
                View All Land
            </button>
            <button
                class="green-button click-cursor"
                @click="() => {
                    this.$refs.landAuction.toggle();
                    this.action = 1
                }"
            >
                Buy Land
            </button>
        </modal>

        <modal
            ref="jesterModal"
            fancyTitle="The Jester"
            height="264"
            width="700"
            :disabled="true"
            :npcDialogue="'Ho there! Looking for adventure, fun, or a bit of both? I know a place not far from here. It\'s not quite ready yet, but be sure to come back in a few weeks, I\'m sure it\'ll be an interesting place for you to visit, hehehe!'"
            :npcName="'Jester Grandle'"
        />

        <land-auction
            ref="landAuction"
            :action="action"
        />
    </div>
</template>

<script>
import './assets/base.css'
import GameButton from './components/GameButton.vue'
import Modal from './components/Modal.vue'
import LandAuction from './components/LandAuction.vue'

export default {
    components: {
        GameButton,
        Modal,
        LandAuction,
    },

    data: () => {
        return {
            scale: 2.2,
            translate: { x: 0, y: 0,},
            scrollTimeout: 0,
            isMouseDown: false,
            isReseting: false,
            currentPosition: {x: 0, y: 0},
            mousePosition: {x: 0, y: 0},
            distance: {x: 0, y: 0},
            stopPosition: {x: 0, y: 0},
            resetInterval: 0,
            resetTimeout: 0,
            action: -1,
        }
    },

    beforeMount() {
        if (window.innerWidth < 376) {
            this.scale = 1;
        }
    },

    mounted() {
        const castleOverlay = this.$refs.castleOverlay;
        // mouse event
        castleOverlay.addEventListener('mousedown', (e) => {
            this.holdEvent(e.clientX, e.clientY);
        })

        window.addEventListener('mouseup', () => {
            this.releaseEvent();
        })

        castleOverlay.addEventListener('mousemove', (e) => {
            this.moveEvent(e.clientX, e.clientY);
        })

        castleOverlay.addEventListener('touchstart', (e) => {
            this.holdEvent(e.touches[0].clientX, e.touches[0].clientY);
        })

        castleOverlay.addEventListener('touchend', () => {
           this.releaseEvent();
        })

        castleOverlay.addEventListener('touchmove', (e) => {
            this.moveEvent(e.touches[0].clientX, e.touches[0].clientY);
        })
    },

    beforeDestroy() {
        const castleOverlay = this.$refs.castleOverlay;

        castleOverlay.removeEventListener('mousemove', ()=>{});
        window.removeEventListener('mouseup', ()=>{});
        castleOverlay.removeEventListener('mousedown', ()=>{});
        castleOverlay.removeEventListener('touchstart', ()=>{});
        castleOverlay.removeEventListener('touchend', ()=>{});
        castleOverlay.removeEventListener('touchmove', ()=>{});
    },

    methods: {
        setCastleTranslate(x, y) {
            this.translate = {
                x: x+'px',
                y: y+'px'
            };
        },

        holdEvent(x, y) {
            this.isMouseDown = true;
            if (this.resetInterval !== 0) {
                clearInterval(this.resetInterval);
                clearTimeout(this.resetTimeout);
                const style = window.getComputedStyle(this.$refs.castle)
                const matrix = new WebKitCSSMatrix(style.transform);
                this.stopPosition.x = matrix.m41;
                this.stopPosition.y = matrix.m42;
            }
            this.currentPosition = {x, y}
        },

        moveEvent(x, y) {
            this.mousePosition.x = x;
            this.mousePosition.y =y;

            if (this.isMouseDown) {
                this.dragCastle(x,y);
            }
        },

        releaseEvent() {
            this.isMouseDown = false;
            this.resetToCenter();
        },

        resetToCenter() {
            clearTimeout(this.scrollTimeout);

            this.resetInterval = setInterval(() => {
                const deviceWidth = window.innerWidth

                let resetPointX = this.scale * 50;

                if (deviceWidth < 376) {
                    resetPointX = this.scale * 180
                } else if (deviceWidth < 769) {
                    resetPointX = this.scale * 150
                } else if (deviceWidth < 1025) {
                    resetPointX = this.scale * 120
                }

                if (
                    (this.scale < 2 && deviceWidth < 1025 && deviceWidth > 376) ||
                    deviceWidth >= 1025
                ) {

                    if (this.distance.x !== 0) {
                        this.distance.x = this.distance.x < 0 ? this.distance.x + 4 : this.distance.x - 4;

                        if (this.distance.x <= 4 && this.distance.x >= -4) {
                            this.distance.x = 0;
                        }
                    }
                } else  {
                    if (this.distance.x <= -resetPointX || this.distance.x >= resetPointX) {
                        this.distance.x = this.distance.x < 0 ? this.distance.x + 4 : this.distance.x - 4;
                    }
                }

                let resetPointY = this.scale * 300;
                if (deviceWidth < 1025) {
                    resetPointY = this.scale * 100
                } else if (deviceWidth < 376) {
                    resetPointY = this.scale * 50;
                }

                if (this.scale < 1.5) {
                    if (this.distance.y !== 0) {
                        this.distance.y = this.distance.y < 0 ? this.distance.y + 4 : this.distance.y - 4;

                        if (this.distance.y <= 4 && this.distance.y >= -4) {
                            this.distance.y = 0;
                        }
                    }
                } else {
                    if (this.distance.y <= -resetPointY || this.distance.y >= resetPointY) {
                        this.distance.y = this.distance.y < 0 ? this.distance.y + 4 : this.distance.y - 4;
                    }
                }

                if (
                    (this.scale < 1.5 ? this.distance.x === 0 : Math.abs(this.distance.x) === resetPointX) &&
                    (this.scale < 1.5 ? this.distance.y === 0 : Math.abs(this.distance.y) === resetPointY)
                ) {
                    clearInterval(this.resetInterval)
                    this.isReseting = false;
                }

                this.isReseting = true;

                this.setCastleTranslate(this.distance.x, this.distance.y);
            })
        },

        zoomCastle(e) {
            const deviceWidth = window.innerWidth

            if (e.deltaY < 0) {
                clearInterval(this.resetInterval);
                clearTimeout(this.scrollTimeout);
                if (
                    (deviceWidth < 376 && this.scale < 2)  ||
                    (deviceWidth >= 376 && this.scale < 3.1)
                ) {
                    this.scale += 0.15;

                    this.distance.x = window.innerWidth/2 - this.mousePosition.x;
                    this.distance.y = window.innerHeight/2 - this.mousePosition.y

                    this.setCastleTranslate(
                        this.distance.x,
                        this.distance.y,
                    );
                }

                this.scrollTimeout = setTimeout(() => {
                    this.resetToCenter();
                }, 500)

            } else if (
                (deviceWidth < 376 && this.scale > 1)  ||
                (deviceWidth >= 376 && this.scale > 1.3)
            ) {
                this.scale -= 0.15;
                this.setCastleTranslate(0, 0);
            }
        },

        dragCastle(x, y) {
            this.distance.x = x - this.currentPosition.x + (this.isReseting ?  this.stopPosition.x : 0);
            this.distance.y = y - this.currentPosition.y + (this.isReseting ?  this.stopPosition.y : 0);


            let limit = 500 * this.scale;

            if (window.innerWidth < 376) {
                limit = 300 * this.scale;
            }

            if (this.distance.x >= limit) {
                this.distance.x = limit
            } else if (this.distance.x <= -limit) {
                this.distance.x = -limit
            }

            if (this.distance.y >= limit) {
                this.distance.y = limit
            } else if (this.distance.y <= -limit) {
                this.distance.y = -limit
            }

            if (this.distance.x !== 0 && this.distance.y !== 0) {
                this.setCastleTranslate(this.distance.x, this.distance.y)
            }
        },
    }
}
</script>