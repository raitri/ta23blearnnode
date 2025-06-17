<script setup>
import { computed, ref } from 'vue';

const cookies = ref(0);
const buildings = ref([
    { name: 'Cursor', price: 15, cps: 0.1, count: 0, upgrades: [
            { name: 'Reinforced Cursor', cost: 100, multiplier: 2, purchased: false },
            { name: 'Carbon Fiber Cursor', cost: 500, multiplier: 3, purchased: false },
        ]},
    { name: 'Grandma', price: 100, cps: 1, count: 0, upgrades: [
            { name: 'Professional Baker', cost: 1000, multiplier: 2, purchased: false },
            { name: 'Industrial Oven', cost: 5000, multiplier: 3, purchased: false },
        ]},
    { name: 'Farm', price: 1000, cps: 10, count: 0, upgrades: [
            { name: 'Organic Fertilizer', cost: 5000, multiplier: 2, purchased: false },
            { name: 'Automated Harvesters', cost: 15000, multiplier: 3, purchased: false },
        ]},
    { name: 'Factory', price: 10000, cps: 100, count: 0, upgrades: [
            { name: 'Quantum Assembly', cost: 50000, multiplier: 2, purchased: false },
            { name: 'Unionized Workplace', cost: 200000, multiplier: 3, purchased: false },
        ]},
]);

const selectedBuilding = ref(null);

function buyBuilding(building){
    cookies.value -= building.price;
    building.price += Math.ceil(building.price / 100 * 15);
    building.count++;
}
function buyUpgrade(upgrade) {
    if (cookies.value < upgrade.cost) return;
    cookies.value -= upgrade.cost;
    upgrade.purchased = true;
}
let cps = computed(() => {
    let cps = 0;
    buildings.value.forEach(building => {
        let multiplier = 1;
        building.upgrades.forEach(upgrade => {
            if (upgrade.purchased) multiplier *= upgrade.multiplier;
        });
        cps += building.cps * building.count * multiplier;
    });
    return cps;
});
setInterval(()=>{
   cookies.value+=cps.value;

   document.title ='🍪' + +cookies.value.toFixed(1) + ' Cookies!';
},1000);

</script>
<template>
    <div class="columns">
        <div class="column is-4 has-background-primary has-text-centered">
            <h1 class="is-size-1">{{ +cookies.toFixed(1) }} cookies</h1>
            <h3 class="is-size-3">{{ +cps.toFixed(1) }} cps</h3>
            <figure class="image is-square" @click="cookies++">
                <img src="https://sweetlorens.com/cdn/shop/products/Copy-of-Chocolate-Chunk-Full-Cookie-transparent-background.png?v=1687811511" />
            </figure>
        </div>
        <div class="column is-6 has-background-link p-4">
            <div v-if="selectedBuilding" class="upgrade-container">
                <h2 class="is-size-3 has-text-centered mb-4">Upgrades for {{ selectedBuilding.name }}</h2>
                
                <div v-for="(upgrade, index) in selectedBuilding.upgrades" 
                     :key="index"
                     class="upgrade-box mb-3 p-3">
                    <div class="is-flex is-justify-content-space-between">
                        <div>
                            <h4 class="is-size-5 mb-2">{{ upgrade.name }}</h4>
                            <p>Multiplier: x{{ upgrade.multiplier }}</p>
                        </div>
                        <button 
                            class="button is-info"
                            :disabled="cookies < upgrade.cost || upgrade.purchased"
                            @click="buyUpgrade(upgrade)">
                            {{ upgrade.purchased ? 'Purchased' : `🍪${upgrade.cost}` }}
                        </button>
                    </div>
                </div>
            </div>
            <div v-else class="has-text-centered mt-6">
                <p class="is-size-4">Select a building to view upgrades</p>
            </div>
        </div>
        <div class="column is-2 has-background-warning">
            <div v-for="(building, index) in buildings" :key="index" class="mb-2">
                <button 
                    :disabled="cookies < building.price" 
                    @click="buyBuilding(building)" 
                    class="button is-primary is-large is-fullwidth">
                    {{ building.name }} 🍪{{ building.price }} #{{ building.count }}
                </button>
                <button 
                    @click="selectedBuilding = building"
                    class="button is-info is-fullwidth mt-1">
                    View Upgrades
                </button>
            </div>
        </div>
    </div>
</template>
<style scoped>
.upgrade-container {
    max-height: 70vh;
    overflow-y: auto;
}

.upgrade-box {
    background-color: rgba(255, 255, 255, 0.1);
    border-radius: 4px;
}
</style>