<template>
    <div
        v-if="isOpen"
        :class="`modal-overlay overlay ${isOpen ? 'active' : ''}`"
    >
        <div
            class="modal game-border fancy"
            :style="{
                width: autoWidth,
                height: height + 'px',
                marginBottom: (npcDialogue ? 30 : 0) + 'px',
            }"
        >
            <div
                class="close-btn click-cursor"
                @click="toggleModal()"
            ></div>

            <h3 class="modal-title fancy" v-if="fancyTitle">
                <span>{{ fancyTitle }}</span>
            </h3>

            <h3 class="modal-title basic" v-if="title">
                <span>{{ title }}</span>
            </h3>

            <div class="modal-body">
                <slot></slot>
            </div>

            <div class="coming-soon__wrap" v-if="disabled">
                <div class="coming-soon">
                    <span>Coming Soon</span>
                </div>
            </div>
        </div>

         <div
            class="npc-dialogue"
            v-if="npcDialogue"
            :style="{
                width: autoWidth,
            }"
        >
                <h4 class="npc-name">
                    <span>{{ npcName }}</span>
                </h4>
                <p>{{ npcDialogue }}</p>
            </div>
    </div>
</template>

<script>
export default {
    props: [
        'fancyTitle',
        'title',
        'buttons',
        'width',
        'height',
        'disabled',
        'npcDialogue',
        'npcName'
    ],

    data() {
        return {
            isOpen: false,
            autoWidth: this.width + 'px',
            windowWidth: window.innerWidth,
        }
    },

    mounted() {
        if (window.innerWidth < this.width) {
            this.autoWidth = '100vw';
        } else {
            this.autoWidth = this.width + 'px';
        }

        this.$nextTick(() => {
            window.addEventListener('resize', this.onResize);
        })
    },

    watch: {
        windowWidth(newWidth) {
            if (newWidth < this.width) {
                this.autoWidth = '100vw';
            } else {
                this.autoWidth = this.width + 'px';
            }
        }
    },

    methods: {
        toggleModal() {
            this.isOpen = !this.isOpen;
        },

        onResize() {
            this.windowWidth = window.innerWidth
        }
    }
}
</script>

<style scoped>
.modal-overlay {
    z-index: -1;
    opacity: 0;
    will-change: opacity;
    transition: all 0.5s linear;
    flex-direction: column;
    align-items: center;
}

.modal-overlay.active {
    z-index: 2000;
    opacity: 1;
    will-change: opacity;
    transition: all 0.5s linear;
}

.modal {
    padding: 50px 50px 70px;
}

.modal-title {
    color: #764d43;
    text-align: center;
    margin-bottom: 0;
}

.modal-title.basic {
    font-size: 28px;
    font-family: "Lora",Georgia,serif;
    font-weight: 400;
}

.modal-title.fancy {
    position: relative;
    top: -42px;
    font-size: 16px;
    margin-top: 30px;
    color: #764d43;
    background-image: url('../assets/images/borders/modal-title-middle.png');
    background-repeat: repeat-x;
    margin: 0 auto;
    height: 70px;
    width: 130px;
    display: flex;
    align-items: center;
    justify-content: center;
}

.modal-title.fancy::before {
    content: "";
    position: absolute;
    display: block;
    width: calc(100% + 60px);
    top: 0;
    left: -30px;
    height: 100%;
    background-image: url('../assets/images/borders/modal-title-left.png'),
        url('../assets/images/borders/modal-title-right.png');
    background-repeat: no-repeat;
    background-position: 0 0,100% 0;
}

.modal-body {
    position: relative;
    z-index: 1;
    display: flex;
    flex-direction: column;
    align-items: center;
}

.coming-soon__wrap {
    width: 100%;
    pointer-events: none;
}

.coming-soon {
    position: relative;
    display: flex;
    align-items: center;
    justify-content: center;
    text-align: center;
    max-width: 300px;
    margin: 6px auto;
    height: 68px;
    max-height: 68px;
    image-rendering: pixelated;
    background-image: url('../assets/images/borders/btn-middle-inactive.png');
    background-repeat: repeat-x;
    background-position: 0 0;
    font-size: 20px;
    font-weight: 700;
    font-family: "Lora",sans-serif;
    color: #744e45;
}

.coming-soon::before {
    content: "";
    display: block;
    width: calc(100% + 80px);
    height: 100%;
    position: absolute;
    top: 0;
    left: -40px;
    background-image: url('../assets/images/borders/btn-left-inactive.png'),
        url('../assets/images/borders/btn-right-inactive.png');
    background-repeat: no-repeat;
    background-position: 0 0,100% 0;
}

.npc-dialogue {
    position: relative;
    padding: 56px 40px 36px;
    image-rendering: pixelated;
    background-color: #ffe3bd;
    background-image: url('../assets/images/borders/npcDialogue-left.png'),
        url('../assets/images/borders/npcDialogue-right.png'),
        url('../assets/images/borders/npcDialogue-top.png'),
        url('../assets/images/borders/npcDialogue-bottom.png')
    ;
    background-repeat: repeat-y,repeat-y,repeat-x,repeat-x;
    background-position: 0 0,100% 0,0 0,0 100%;
    border-radius: 25px
}

.npc-dialogue::before {
    content: "";
    display: block;
    width: 100%;
    height: 100%;
    position: absolute;
    top: 0;
    left: 0;
    background-image: url('../assets/images/borders/npcDialogue-top-left.png'),
        url('../assets/images/borders/npcDialogue-top-right.png'),
        url('../assets/images/borders/npcDialogue-bottom-left.png'),
        url('../assets/images/borders/npcDialogue-bottom-right.png')
    ;
    background-repeat: no-repeat;
    background-position: 0 0,100% 0,0 100%,100% 100%;
}

.npc-dialogue p {
    margin: 0;
    font-size: 16px;
    color: #744e45;
}

.npc-name {
    position: absolute;
    top: -18px;
    left: 80px;
    margin: 0;
    background-image:  url('../assets/images/borders/npcName-middle.png');
    background-repeat: repeat-x;
    height: 58px;
    line-height: 58px;
    padding: 0 20px;
    white-space: nowrap;
}

.npc-name span {
    position: relative;
    font-family: "Lora",Georgia,serif;
    color: #744e45;
    font-size: 26px;

}

.npc-name::before {
    content: "";
    display: block;
    position: absolute;
    width: calc(100% + 60px);
    top: 0;
    left: -30px;
    height: 100%;
    background-image: url('../assets/images/borders/npcName-left.png'),
        url('../assets/images/borders/npcName-right.png');
    background-repeat: no-repeat;
    background-position: 0 0,100% 0;
}

@media (max-width: 590px) {
    .modal,
    .npc-dialogue {
        width: 100vw !important;
    }

    .modal-title.fancy {
        top: 17px;
    }

    .modal-body {
        top: 38px;
    }

    .close-btn {
        top: 35px;
        right: 28px;
        width: 48px;
        height: 48px;
    }

    .modal-button {
        border-radius: 9px;
        width: 200px;
        height: 41px;
        font-size: 14px;
    }

    .coming-soon {
        width: 200px;
        top: 14px;
    }
}
</style>