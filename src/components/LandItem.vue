<template>
    <div>
        <div class="land-item">
            <!-- land image -->
            <div class="land-image-wrap">
                <div
                    class="land-image"
                    :style="{
                        backgroundImage: `url('src/assets/images/lands/land-${land.id}.png')`
                    }"
                >
                    <div
                        class="green-button land-sale-tag"
                        v-if="land.status === 0"
                    >
                        For sale
                    </div>
                </div>
            </div>


            <div class="land-item__details">
                <!-- header -->
                <div class="land-item__header">
                    <p>{{ land.name }}</p>
                    <p>#{{ land.id }}</p>
                </div>

                <!-- info -->
                <div class="land-info">
                    <div class="land-info__row">
                        <p>{{ regions[land.region] }}</p>
                        <p>Region {{ land.region }}</p>
                    </div>
                    <div class="land-info__row">
                        <p>Level</p>
                        <p>{{ land.level }}</p>
                    </div>
                    <div
                        class="land-info__row"
                        v-if="land.status === 0 && action === 0"
                        style="font-weight: bold"
                    >
                        <p>For sale</p>
                        <p>{{ land.price }}</p>
                    </div>
                    <div
                        class="land-info__row"
                        v-if="land.status !== 0 && action === 0"
                    >
                        <p>Not for sale</p>
                    </div>
                </div>
            </div>

            <div
                class="land-item__price"
                v-if="action === 1"
            >
                <img src="../assets/images/jewel.png" alt="">
                <p>{{ land.price }}</p>
            </div>

            <!-- Button -->
            <div class="land-item__butotn-wrap">
                <button
                    class="green-button click-cursor"
                    v-if="action === 0"
                    @click="this.$refs.landDetailModal.toggleModal()"
                >
                    View Details
                </button>
                <button
                    class="green-button click-cursor"
                    v-if="action === 1"
                    @click="this.$refs.landDetailModal.toggleModal()"
                >
                    Buy Land
                </button>
            </div>
        </div>

        <!-- land detail modal -->
        <modal
            ref="landDetailModal"
            :title="land.name"
            width="420"
        >
            <h3 class="land-id">Land #{{ land.id }}</h3>
            <h4 class="land-region">{{ regions[land.region] }}</h4>

            <div class="land-level">
                <p>Level</p>
                <p>{{ land.level }}</p>
            </div>

            <div
                class="land-item__price land-price"
                v-if="action === 1"
            >
                <img src="../assets/images/jewel.png" alt="">
                <p>{{ land.price }}</p>
            </div>

            <div
                class="land-purchase"
                v-if="action === 1"
            >
                <button class="green-button click-cursor">
                    Approve Contract
                </button>
            </div>

            <div class="land-owner">
                <p>Ruled by: {{ land.owner.name }}</p>
                <p>{{ land.owner.walletAddress }}</p>
            </div>
        </modal>
    </div>
</template>

<script>
import Modal from './Modal.vue'

export default {
    props: [
        'land',
        'action'
    ],

    components: {
        Modal
    },

    data() {
        return {
            regions: [
                'Adelyn',
                'Adelyn Highlands',
                'Amber Town',
                'Amoriath\'s Domain',
                'Breakwater Island',
                'Dornielle Abbey',
                'Esterfork',
                'Fairling Forest',
                'Gaeron\'s Wood',
                'Haywoord',
                'Illyria\'s Rest',
                'Lake of Knives',
                'Old King\'s Barrow',
                'Riverhold',
                'Stefan\'s Lake',
                'Stillwood Meadow'
            ],
        }
    },
}
</script>

<style scoped>
.land-item {
    height: auto;
    background-color: #eecba1;
    margin-bottom: 15px;
    padding: 16px 16px 24px;
    border-radius: 8px;
    display: flex;
    flex-direction: column;
    justify-content: space-between;
}

.land-image-wrap {
    width: 100%;
    height: 0;
    padding-bottom: 100%;
    margin-bottom: 18px;
    position: relative;
    border-radius: 8px 2px 8px 2px;
    background-image: url('../assets/images/borders/inventory-itemFrameLeft.png'),
        url('../assets/images/borders/inventory-itemFrameRight.png');
    background-position: 0 0,100% 0;
    background-repeat: repeat-y,repeat-y;
}

.land-image-wrap::before,
.land-image-wrap::after {
    position: absolute;
    content: "";
    height: calc(100% + 6px);
}

.land-image-wrap::before {
    top: -3px;
    left: 50%;
    transform: translateX(-50%);
    width: calc(100% - 6px);
    border-radius: 8px 2px 8px 2px;
    background-image: url('../assets/images/borders/inventory-itemFrameTop.png'),
        url('../assets/images/borders/inventory-itemFrameBtm.png');
    background-position: top,bottom;
    background-repeat: repeat-x,repeat-x;
}

.land-image-wrap::after {
    top: -3px;
    left: 0;
    width: 100%;
    background-image: url('../assets/images/borders/inventory-itemFrameTopLeft.png'),
        url('../assets/images/borders/inventory-itemFrameTopRight.png'),
        url('../assets/images/borders/inventory-itemFrameBtmLeft.png'),
        url('../assets/images/borders/inventory-itemFrameBtmRight.png');
    background-position: 0 0,100% 0,0 100%,100% 100%;
    background-repeat: no-repeat;
}

.land-image {
    position: absolute;
    width: calc(100% - 6px);
    height: calc(100% - 1px);
    top: 0;
    left: 3px;
    border-radius: 8px 2px 8px 2px;
    background-position: center center;
    background-size: cover;
    background-repeat: no-repeat;
    overflow: hidden;
}

.land-item__header {
    display: flex;
    justify-content: space-between;
    font-family: "Lora",Georgia,serif;
    font-size: 15px;
    font-weight: 700;
    color: #3e1f05;
}

.land-info {
    font-size: 13px;
    color: rgba(62,31,5,.8);
}

.land-info__row {
    display: flex;
    justify-content: space-between;
}

.land-info__row p {
    margin: 0;
}

.land-item__butotn-wrap {
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: flex-end;
}

.land-sale-tag {
    position: absolute;
    top: -12px;
    left: 50%;
    padding-top: 10px;
    padding-bottom: 4px;
    font-size: 16px;
    background-color: #ffe3bd;
    color: #3e1f05;
    white-space: nowrap;
    transform: translateX(-50%);
    opacity: 1;
    text-align: center;
}

.land-item__price {
    display: flex;
    justify-content: center;
    align-items: center;
}

.land-item__price p {
    font-size: 24px;
    font-weight: 600;
    margin: 0;
    color: #3e1f05;
    font-family: Poppins;
}

.land-item__price img {
    width: 14px;
    height: 14px;
    margin-right: 6px;
}

.modal .land-id {
    margin: 0;
    font-weight: 400;
    color: #3e1f05;
    font-family: Poppins;
}

.modal .land-region {
    margin: 5px auto;
    font-weight: 400;
    font-size: 14px;
    color: #3e1f05;
    font-family: Poppins;
}

.modal p {
    color: #3e1f05;
    margin: 0;
}

.modal .land-level {
    display: flex;
    align-items: center;
    justify-content: space-between;
    padding: 12px 16px;
    margin: 32px auto 12px;
    width: 80%;
    max-width: 300px;
    border-radius: 8px;
    background-color: #efcba2;
}

.modal .land-level p:first-child {
    font-family: "Lora", Georgia,serif;
    font-weight: 400;
}

.modal .land-owner {
    text-align: center;
    margin: 32px 0 16px;
}

.modal .land-owner p:first-child {
    font-weight: 600;
}

.modal .land-owner p:nth-child(2)  {
    font-size: 12px;
}

.modal .ldan-price {
    margin: 16px 0 0;
}

</style>