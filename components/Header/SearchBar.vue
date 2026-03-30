
<template>
    <div class="searc d-none d-xl-block d-lg-block pr-3" style="position:relative;">
        <input
            type="search"
            class="search"
            v-model="query"
            @input="onInput"
            @keydown.down.prevent="moveSelection(1)"
            @keydown.up.prevent="moveSelection(-1)"
            @keydown.enter.prevent="selectSuggestion"
            autocomplete="off"
            placeholder="Rechercher..."
        >
        <ul v-if="showSuggestions && filteredSuggestions.length" class="autocomplete-list">
            <li
                v-for="(suggestion, idx) in filteredSuggestions"
                :key="suggestion"
                :class="{ selected: idx === selectedIndex }"
                @mousedown.prevent="selectSuggestion(idx)"
            >
                {{ suggestion }}
            </li>
        </ul>
    </div>
</template>

<script setup>
import { ref, computed } from 'vue';

// Suggestions fictives, à remplacer par une API si besoin
const suggestions = [
    'iPhone',
    'iPad',
    'iMac',
    'AirPods',
    'Apple Watch',
    'MacBook',
    'Samsung Galaxy',
    'Sony Xperia',
    'Huawei P50',
    'Xiaomi Redmi',
];

const query = ref('');
const showSuggestions = ref(false);
const selectedIndex = ref(-1);

const filteredSuggestions = computed(() => {
    if (!query.value) return [];
    return suggestions.filter(s =>
        s.toLowerCase().includes(query.value.toLowerCase())
    );
});

function onInput() {
    showSuggestions.value = !!filteredSuggestions.value.length;
    selectedIndex.value = -1;
}

function moveSelection(direction) {
    if (!filteredSuggestions.value.length) return;
    if (selectedIndex.value === -1 && direction === 1) {
        selectedIndex.value = 0;
        return;
    }
    let next = selectedIndex.value + direction;
    if (next < 0) next = filteredSuggestions.value.length - 1;
    if (next >= filteredSuggestions.value.length) next = 0;
    selectedIndex.value = next;
}

function selectSuggestion(idx) {
    const i = typeof idx === 'number' ? idx : selectedIndex.value;
    if (i >= 0 && filteredSuggestions.value[i]) {
        query.value = filteredSuggestions.value[i];
        showSuggestions.value = false;
    }
}
</script>
<style scoped lang="scss">
.search {
    outline: none;
    border: 1px #F8F8F8;
    background: #ededed url('@/assets/search.png') no-repeat 5px center;
    padding: 5px 8px 4px 26px;
    width: 10px;
    border-radius: 10em;
    transition: all .5s;
    margin-right: 10px;

    &:focus {
        width: 160px;
        font-family: inherit;
        padding-left: 30px;
        border: solid 1px #ccc;
        background-color: #fff;
        border-color: #98ccfd;
        box-shadow: 0 0 5px rgba(109, 207, 246, .5);
        backface-visibility: hidden;
        perspective: 1000;
    }
}

.autocomplete-list {
    position: absolute;
    top: 100%;
    left: 0;
    right: 0;
    background: #fff;
    border: 1px solid #ccc;
    border-radius: 0 0 10px 10px;
    z-index: 10;
    margin: 0;
    padding: 0;
    list-style: none;
    max-height: 200px;
    overflow-y: auto;
}
.autocomplete-list li {
    padding: 8px 16px;
    cursor: pointer;
}
.autocomplete-list li.selected,
.autocomplete-list li:hover {
    background: #f0f8ff;
}
</style>
