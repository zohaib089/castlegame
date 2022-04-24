<template>
    <div
        :class="`land-auction overlay row ${isOpen ? 'active' : ''}`"
    >
        <!-- Filter -->
        <land-filter
            :selectedStatus="filterStatus"
            :selectedRegions="filterRegions"
            @updateFilterStatus="onUpdateFilterStatus"
            @updateFilterRegion="onUpdateFilterRegions"
            @updateFilterSearchId="onUpdateFilterSearchId"
            @applyFilter="filterLand"

        />

        <!-- Land display -->
        <land-view
            :closeLandAuction="() => toggle()"
            :action="action"
            :lands="filteredLands"
            :key="filteredLands"
        />
    </div>
</template>

<script>
import LandFilter from './LandFilter.vue';
import LandView from './LandView.vue';


export default {
    props: ['action'],

    components: {
      LandFilter,
      LandView,
    },

    data() {
        return {
            isOpen: false,
            lands: [
                {
                    id: 1,
                    name: 'Land 1',
                    region: 0,
                    level: 1,
                    status: 1, // 0: for sale, 1: not for sale, 2: vacant
                    price: 0,
                    owner: {
                        name: 'User 1',
                        walletAddress: '0x000000000000000000000000000000000001',
                    }
                },
                {
                    id: 2,
                    name: 'Land 2',
                    region: 0,
                    level: 1,
                    status: 1,
                    price: 0,
                    owner: {
                        name: 'User 2',
                        walletAddress: '0x000000000000000000000000000000000002',
                    }
                },
                {
                    id: 3,
                    name: 'Land 3',
                    region: 1,
                    level: 1,
                    status: 0,
                    price: 300000,
                    owner: {
                        name: 'User 3',
                        walletAddress: '0x000000000000000000000000000000000003',
                    }
                },
                {
                    id: 4,
                    name: 'Land 4',
                    region: 1,
                    level: 1,
                    status: 0,
                    price: 400000,
                    owner: {
                        name: 'User 4',
                        walletAddress: '0x000000000000000000000000000000000004',
                    }
                },
                {
                    id: 5,
                    name: 'Land 5',
                    region: 2,
                    level: 1,
                    status: 1,
                    price: 0,
                    owner: {
                        name: 'User 5',
                        walletAddress: '0x000000000000000000000000000000000005',
                    }
                },
            ],
            filterSearchId: '',
            filterStatus: -1,
            filterRegions: [],
            filteredLands: [],
        }
    },

    beforeMount() {
        this.filterLand();
    },

    watch: {
        action() {
            this.filterLand();
        }
    },

    methods: {
        toggle() {
            this.isOpen = !this.isOpen;
        },

        filterLand() {
            this.filteredLands = [...this.lands]

            if (this.action === 1)  {
                this.filteredLands = this.filteredLands.filter(land => land.status == 0)
            }

            if (this.filterSearchId !== '') {
                this.filteredLands = this.filteredLands.filter(land => land.id === parseInt(this.filterSearchId, 10))
            }

            if (this.filterStatus != -1) {
                this.filteredLands = this.filteredLands.filter(land => land.status == this.filterStatus)
            }

            if (this.filterRegions.length !== 0) {
                let temp = [];
                this.filterRegions.forEach(region => {
                    temp = [...temp, ...this.filteredLands.filter(land => land.region == region)]
                })

                this.filteredLands = [...temp];
            }
        },

        onUpdateFilterStatus(status) {
            this.filterStatus = status;
        },

        onUpdateFilterRegions(regions) {
            this.filterRegions = [...regions];
        },

        onUpdateFilterSearchId(id) {
            this.filterSearchId = id
        }
    },
}
</script>

<style scoped>
.land-auction {
    z-index: -1;
    opacity: 0;
    will-change: opacity;
    transition: all 0.5s linear;
    margin: 0;
    padding: 60px 3% 30px;
    box-sizing: border-box;
}

.land-auction.active {
    z-index: 3000;
    opacity: 1;
    will-change: opacity;
    transition: all 0.5s linear;
}

@media (max-width: 1024px) {
    .land-auction {
        margin-bottom: 0;
        align-items: unset;
        margin-top: 0;
        padding-top: 30px;
        overflow-x: hidden;
    }
}
</style>