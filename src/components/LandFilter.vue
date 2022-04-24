<template>
    <div
        class="filter col c-12 m-6 l-4"
        :class="!isExpanded ? '' : 'active'"
        :key="isMobile"
    >
        <div
            class="filter-expanded game-border basic"
            v-if="isMobile || isExpanded"
        >
            <!-- Header -->
            <div class="filter-header">
                <div class="filter-header-group">
                    <span>Filter</span>
                    <div
                        class="filter-toggle-btn click-cursor"
                        @click="expandFilter()"
                    >
                        <i v-if="!isMobile" class="fas fa-angle-left"></i>
                        <i v-if="isMobile && !isExpanded" class="fas fa-caret-down"></i>
                        <i v-if="isMobile && isExpanded" class="fas fa-caret-up"></i>
                    </div>
                </div>
                <div
                    v-if="isExpanded"
                    class="filter-header-group"
                >
                    <button
                        class="green-button click-cursor filter-btn"
                        @click="applyFilter"
                    >
                        Apply
                    </button>

                    <button
                        class="green-button click-cursor filter-btn"
                        @click="resetFilter"
                    >
                        Reset
                    </button>
                </div>
            </div>
            <hr>

            <!-- Body -->
            <div
                class="filter-body game-scroll-bar"
                v-if="isExpanded"
            >
                <!-- Search -->
                <div class="filter-search">
                    <input
                        v-model="landSearchId"
                        type="text"
                        class="filter-search-input"
                        placeholder="Search by Land ID"
                        @change="$emit('updateFilterSearchId', landSearchId)"
                    >
                </div>

                <!-- Status -->
                <div class="filter-status">
                    <span class="filter-heading">Status</span>
                    <div class="container">
                        <ul class="filter-status__list row">
                            <li
                                class="filter-status__item col c-6"
                                v-for="(status, index) in filterStatus"
                                :key="status"
                            >
                                <label class="status-label click-cursor" :for="`status-${index -1}`">
                                    <input
                                        class="click-cursor"
                                        type="radio"
                                        name="status"
                                        :value="index - 1"
                                        :id="`status-${index - 1}`"
                                        :checked="index === 0"
                                        @change="(e) => checkFilterStatus(e)"
                                    />
                                    <span>{{ status }}</span>
                                </label>
                            </li>
                        </ul>
                    </div>
                </div>

                <!-- Region -->
                <div class="filter-region">
                    <span class="filter-heading">Region</span>
                    <div class="container">
                        <ul class="filter-region__list row">
                            <li
                                class="filter-region__item col c-6"
                                v-for="(region, index) in filterRegion"
                                :key="region"
                            >
                                <label
                                    class="region-label click-cursor"
                                    :for="`region-${index - 1}`"
                                >
                                    <input
                                        class="click-cursor region-checkbox"
                                        type="checkbox"
                                        name="regions"
                                        :id="`region-${index - 1}`"
                                        :checked="index === 0"
                                        :value="index - 1"
                                        @change="(e) => checkFilterRegion(e)"
                                    />
                                    <span>{{ region }}</span>
                                </label>
                            </li>
                        </ul>
                    </div>
                </div>
            </div>
        </div>

        <div
            class="filter-shrink game-border basic"
             v-if="!isExpanded && !isMobile"
        >
            <div
                class="filter-expand-btn click-cursor"
                @click="expandFilter()"
            >
                <i class="fas fa-sliders-h"></i>
            </div>
        </div>
    </div>
</template>

<script>
import '../assets/grid.css'

export default {
    props: {
        selectedStatus: Function,
        selectedRegions: Function,
    },

    data() {
        return {
            isExpanded: true,
            isMobile: false,
            filterStatus: ['All', 'For Sale', 'Not for sale', 'Vacant'],
            filterRegion: [
                'All',
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
            tempSelectedRegions: [],
            landSearchId: "",
            windowWidth: window.innerWidth,
        }
    },

    mounted() {
        if (this.windowWidth < 740) {
            this.isMobile = true;
        } else {
            this.isMobile = false;
        }

        this.$nextTick(() => {
            window.addEventListener('resize', this.onResize);
        })
    },

    watch: {
        windowWidth(newWidth) {
            if (newWidth < 740) {
                this.isMobile = true;
            } else {
                this.isMobile = false;
            }
        }
    },

    methods: {
        expandFilter() {
            this.isExpanded = !this.isExpanded;
        },

        checkFilterRegion(e) {
            const filterCheckBoxes = document.querySelectorAll('.region-checkbox');

            if (e.target.value === '-1') {
                if (e.target.checked) {
                    this.tempSelectedRegions = [];
                    Array.from(filterCheckBoxes).forEach((checkbox, index) => {
                        if (index !== 0)
                            checkbox.checked = false
                    });
                } else {
                    e.target.checked = true;
                }
            } else {
                const checkBoxFilted = Array.from(filterCheckBoxes).filter(checkBox => checkBox.checked);
                document.querySelector('#region--1').checked = checkBoxFilted.length === 0;

                if (e.target.checked) {
                    this.tempSelectedRegions.push(e.target.value);
                } else {
                    for(let i = 0; i < this.tempSelectedRegions.length; i++) {
                        if ( this.tempSelectedRegions[i] === e.target.value) {
                            this.tempSelectedRegions.splice(i, 1);
                            i--;
                        }
                    }
                }
            }

            this.$emit('updateFilterRegion', this.tempSelectedRegions)
        },

        checkFilterStatus(e) {
            if (e.target.checked) {
                this.$emit('updateFilterStatus', e.target.value)
            }
        },

        applyFilter() {
            this.$emit('applyFilter')

            if (window.innerWidth < 376) {
                this.expandFilter();
            }
        },

        resetFilter() {
            document.querySelector('#region--1').click();
            document.querySelector('#status--1').click();

            this.landSearchId = "";
            this.$emit('applyFilter')
        },

        onResize() {
            this.windowWidth = window.innerWidth
        }
    }
}
</script>

<style scoped>
.filter {
    height: 100%;
    display: flex;
    justify-content: flex-end;
}

.filter-expanded {
    height: 100%;
    width: 100%;
    padding: 74px 50px 88px;
}

.filter-shrink {
    width: 118px;
    height: 156px;
    display: flex;
    justify-content: center;
    align-items: center
}

.filter-expand-btn {
    position: relative;
    z-index: 1;
    font-size: 20px;
}

.filter-header-group {
    font-size: 18px;
    color: #3e1f05;
    position: relative;
    z-index: 1;
    display: flex;
    justify-content: space-between;
    align-items: center;
}

.filter-toggle-btn {
    font-size: 20px;
    padding: 0.5em 0;
    padding-left: 20px;
}

.filter-btn {
    width: 48%;
    margin: 0;
}

.filter-body {
    position: relative;
    height: calc(100% - 100px);
    overflow-y: auto;
    overflow-x: hidden;
    padding: 0 3px 3px;
    margin: 1em 0 0;
}

.filter-search {
    padding-bottom: 1em;
}

.filter-search-input {
    background-color: #eecba1;
    color: #3e1f05;
    height: 36px;
    width: 100%;
    outline: none;
    border: none;
    border-radius: 8px;
    padding: 3px 10px;
    font-family: Poppins;
    font-size: 18px;
    font-weight: 500;
}

.filter-search-input::placeholder {
    color: #c7a47d;
}

.filter-search-input:focus {
    border-width: 2px;
    border-style: solid;
    border-color: #dcbe98;
}

.filter-heading {
    font-family: "Lora", Georgia,serif;
    font-size: 18px;
    color: #3e1f05;
    font-weight: 700;
}

.filter-status__item,
.filter-region__item {
    margin-bottom: 10px;
}

.status-label,
.region-label {
    color: #3e1f05;
    font-family: "Lora", Georgia,serif;
    display: flex;
    align-items: center;
    /* width: fit-content; */
}

.status-label span,
.region-label span {
    display: block;
    overflow: hidden;
    white-space: nowrap;
    text-overflow: ellipsis;
    max-width: 142px;
}

.status-label input,
.region-label input {
    position: relative;
    margin: 0;
    margin-right: 5px;
}

.status-label input::after,
.region-label input::after {
    content: "";
    position: absolute;
    top: -1px;
    left: -1px;
    right: -1px;
    bottom: -1px;
    background-color: #ffe3bd;
    border-width: 1px;
    border-style: solid;
    border-color: #000;
}

.status-label input::after,
.status-label input::before {
    border-radius: 50%;
}

.status-label input:checked::before,
.region-label input:checked::before {
    content: "";
    position: absolute;
    top: 1px;
    left: 1px;
    right: 1px;
    bottom: 1px;
    background-color: #066c45;
    z-index: 1;
}

@media (max-width: 1024px) {
    .filter {
        height: 20%;
    }
    .filter.active {
        height: auto;
    }
}

@media (max-width: 376px) {
    .filter {
        height: 33%;
        margin-bottom: 15px;
    }

}

</style>