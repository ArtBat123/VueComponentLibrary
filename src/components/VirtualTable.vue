<template>
    <div
        ref="scrollElementRef"
        :style="containerStyle"
        @scroll="onScroll"
    >
        <table
            :style="{ height: items.length * itemHeight + 'px' }"
            style="table-layout: fixed; width: 100%"
        >
            <thead style="position: sticky; top: 0; background-color: azure">
                <tr>
                    <th
                        v-for="head in headers"
                        :style="{ width: head.width }"
                    >
                        {{ head.header }}
                    </th>
                </tr>
            </thead>
            <tbody :style="viewportStyle">
                <tr v-for="item in renderedItems">
                    <td
                        v-for="head in props.headers"
                        style="border: 1px solid black"
                    >
                        {{ item[head.field] }}
                    </td>
                </tr>
                <tr>
                    <td :style="{ height: offsetBottomHeight + 'px' }"></td>
                </tr>
            </tbody>
        </table>
    </div>
</template>
<script setup lang="ts">
import { computed, ref } from 'vue';

interface Props {
    items: Array<any>;
    itemHeight: number;
    headers: Array<Object>;
    numToleratedItems?: number;
    scrollHeight: number;
}

const props = withDefaults(defineProps<Props>(), { numToleratedItems: 5 });
const scrollElementRef = ref<HTMLDivElement>();

let renderedItems = ref(
    props.items.slice(0, Math.ceil(props.scrollHeight / props.itemHeight) + props.numToleratedItems)
);
let offsetTopHeight = ref(0);
let offsetBottomHeight = ref(0);

function onScroll() {
    if (!scrollElementRef.value) return;

    const scrollTop = scrollElementRef.value.scrollTop;

    let startItemIndex = Math.floor(scrollTop / props.itemHeight);
    let endItemIndex = Math.ceil((scrollTop + props.scrollHeight) / props.itemHeight);

    startItemIndex = Math.max(0, startItemIndex - props.numToleratedItems);
    endItemIndex = Math.min(props.items.length - 1, endItemIndex + props.numToleratedItems);

    renderedItems.value = props.items.slice(startItemIndex, endItemIndex);
    offsetTopHeight.value = startItemIndex * props.itemHeight;
    offsetBottomHeight.value =
        props.items.length * props.itemHeight - renderedItems.value.length * props.itemHeight;
}

const viewportStyle = computed(() => ({
    transform: `translate3d(0px, ${offsetTopHeight.value}px, 0px)`,
}));
const containerStyle = computed(() => ({
    height: props.scrollHeight + 'px',
    overflow: 'auto',
    position: 'relative',
}));
</script>
<style scoped>
table,
td,
th {
    border: 1px solid black;
    border-collapse: collapse;
}
td {
    border: 1px solid red;
}
</style>
