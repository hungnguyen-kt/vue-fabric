<template>
    <div class="bottom-bar">
        <button class="btn btn-remove" @click="$emit('remove')">Remove</button>
        <input
            class="range"
            type="range"
            v-model="filter"
            min="-1"
            max="1"
            step="0.003921"
            @change="handleFilter()"
        >
    </div>
</template>

<script>
export default {
    name: 'BottomBar',
    data() {
        return {
            filter: 0.1
        }
    },

    methods: {
        handleFilter() {
            this.$emit('filters', this.filter)
        }
    }
}
</script>

<style>
.bottom-bar {
    position: absolute;
    bottom: 0;
    left: 0;
    z-index: 100;
    width: 100%;
    height: auto;
    background-color: rgba(0, 0, 0, 0.3);
    backdrop-filter: blur(3px);
    display: flex;
    flex-direction: row;
    justify-content: space-between;
    padding: 8px;
    box-sizing: border-box;
    align-content: center;
    align-items: center;
}



.bottom-bar .btn-remove {
    color: #FFF;
    border-color: rgb(220 53 69);
}
</style>
