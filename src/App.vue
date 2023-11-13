<template>
    <div>
        <VirtualTable
            :headers="headers"
            :items="items"
            :item-height="24"
            :scroll-height="500"
        ></VirtualTable>
    </div>
</template>

<script setup lang="ts">
import VirtualTable from './components/VirtualTable.vue';

const items: Array<Object> = [];
const headers = [
    {
        field: 'code',
        header: 'code',
        width: '200px',
    },
    {
        field: 'name',
        header: 'name',
        width: '200px',
    },
];
for (let i = 0; i <= 100; i++) {
    items.push({
        code: i,
        name: 'name' + i,
    });
}
</script>
