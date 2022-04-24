<template>
    <div class="land-view-container col c-12 m-6 l-8 game-border fancy">
        <div
            class="close-btn click-cursor"
            @click="closeLandAuction"
        ></div>

        <!-- Heading -->
        <h2 class="land-view__heading">
            {{ getTitle() }}
        </h2>

        <!-- Summary & Sort -->
        <div class="summry-sort-land">
            <div class="land-summary">
                <span>
                    Showing {{ lands.length }} lands
                </span>
            </div>

            <div class="land-sort">
                <span class="land-sort__label">Sort by: </span>

                <div
                    class="land-select__list"
                    id="landPropertyLabel"
                >
                    <select
                        v-model="landPropertySort"
                        class="click-cursor"
                        name="landProperty"
                        id="landProperty"
                        @change="sortLand"
                    >
                        <option value="0">ID</option>
                        <option value="1">Name</option>
                        <option value="2">Region</option>
                        <option value="3">Level</option>
                        <option value="4">Sale Price</option>
                    </select>
                </div>

                <div
                    class="land-select__list"
                >
                    <select
                        v-model="landSortType"
                        class="click-cursor"
                        name="sortType"
                        id="sortType"
                        @change="sortLand"
                    >
                        <option value="0">Ascending</option>
                        <option value="1">Descending</option>
                    </select>
                </div>
            </div>
        </div>

        <hr>

        <!-- Land List -->
        <land-list
            :lands="lands"
            :action="action"
            :key="lands"
        />
    </div>
</template>

<script>
import LandList from './LandList.vue'

export default {
    components: {
        LandList,
    },

    props: [
        'closeLandAuction',
        'action',
        'lands'
    ],

    data() {
        return {
            landPropertySort: '4',
            landSortType: '0'
        }
    },

    beforeMount() {
        this.sortLand();
    },

    methods: {
        getTitle() {
            return this.action === 0 ? 'View All Land' : 'Buy Land'
        },

        sortLand() {
            switch (this.landPropertySort) {
                case '0':
                    this.lands.sort((a, b) => {
                        return a.id - b.id;
                    })
                    break;
                 case '1':
                    this.lands.sort((a, b) => {
                        return a.name - b.name;
                    })
                    break;
                 case '2':
                    this.lands.sort((a, b) => {
                        return a.region - b.region;
                    })
                    break;
                case '3':
                    this.lands.sort((a, b) => {
                        return a.level - b.level;
                    })
                    break;
                 case '4':
                    this.lands.sort((a, b) => {
                        return a.price - b.price;
                    })
                    break;
            }

            if (this.landSortType == 0) {
                this.lands.reverse();
            }
        }
    }
}
</script>

<style scoped>
.land-view-container {
    height: 100%;
    padding: 50px 75px 60px;
    display: flex;
    flex-direction: column;
}

.land-view__heading {
    text-align: center;
    font-family: "Lora",Georgia,serif;
    font-size: 28px;
    font-weight: 400;
    color: #3e1f05;
    margin-top: 0;
    margin-bottom: 18px;
}

.summry-sort-land {
    position: relative;
    z-index: 1;
    display: flex;
    justify-content: space-between;
    align-items: center;
    color: #3e1f05;
}

.land-summary {
    font-size: 18px;
}

.land-sort {
    display: flex;
    align-items: center;
}

.land-sort__label {
    font-size: 14px;
}

.land-select__list {
    background-color: #eecba1;
    border-radius: 8px;
    box-shadow: 0 0 3px rgb(62 31 5 / 50%);
    position: relative;
}

#landPropertyLabel {
    margin: 0 8px;
}

.land-select__list select {
    padding: 8px 30px 8px 5px;
    min-width: 130px;
    background-color: transparent;
    outline: none;
    border: none;
    appearance: none;
}

.land-select__list select::-ms-expand {
    display: none;
}

.land-select__list select option {
    background-color: #eecba1;
    color: #3e1f05;
}

.land-select__list::after {
    position: absolute;
    content: "";
    top: 14px;
    right: 3px;
    border-width: 4px;
    border-style: solid;
    border-color: currentColor transparent transparent transparent;
    opacity: 0.5;
}

.land-select__list:hover::after {
    opacity: 1;
}

@media (max-width: 1024px) {
    .land-view-container {
        padding: 115px 55px 70px;
    }

    .summry-sort-land,
    .land-sort {
        flex-direction: column;
        align-items: flex-start;
        width: 100%
    }

    .land-summary {
        margin-bottom: 8px;
    }

    .land-sort {

    }

    #landPropertyLabel {
        margin: 0;
    }

    .land-select__list {
        width: 100%;
        margin: 6px 0 !important;
    }

    .land-select__list select {
        padding: 8px 30px 8px 5px;
        min-width: 100%;
        background-color: transparent;
        outline: none;
        border: none;
        appearance: none;
    }
}

@media (max-width: 376px) {
    .land-view-container {
        height: 900px;
        padding: 115px 55px 70px;
    }
}

</style>