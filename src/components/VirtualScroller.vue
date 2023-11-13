<template>
    <div
        ref="scrollElementRef"
        :style="containerStyle"
        @scroll="onScroll"
    >
        <div :style="{ height: totalHeight + 'px' }">
            <div :style="viewportStyle">
                <template v-for="item in renderedItems">
                    <slot
                        name="item"
                        :item="item"
                    ></slot>
                </template>
            </div>
        </div>
    </div>
</template>
<script setup lang="ts">
import { computed, ref, useSlots } from 'vue';

interface Props {
    items: Array<Object>;
    scrollHeight: number;
    itemHeight: number;
    headerHeight?: number;
    numToleratedItems?: number;
}
const props = withDefaults(defineProps<Props>(), {
    numToleratedItems: 5,
});
const slots = useSlots();
const scrollElementRef = ref<HTMLDivElement>();

let renderedItems = ref(
    props.items.slice(0, Math.ceil(props.scrollHeight / props.itemHeight) + props.numToleratedItems)
);
let offsetHeight = ref(0);

function onScroll() {
    if (!scrollElementRef.value) return;

    const scrollTop = scrollElementRef.value.scrollTop;
    let startItemIndex = Math.floor(scrollTop / props.itemHeight);
    let endItemIndex = Math.ceil((scrollTop + props.scrollHeight) / props.itemHeight);
    startItemIndex = Math.max(0, startItemIndex - props.numToleratedItems);
    endItemIndex = Math.min(props.items.length - 1, endItemIndex + props.numToleratedItems);
    renderedItems.value = props.items.slice(startItemIndex, endItemIndex);
    offsetHeight.value = startItemIndex * props.itemHeight;
}

const viewportStyle = computed(() => ({
    transform: `translate3d(0px, ${offsetHeight.value}px, 0px)`,
}));
const containerStyle = computed(() => ({ height: props.scrollHeight + 'px', overflow: 'auto' }));
const totalHeight = computed(() => {
    const headerHeight = slots.header && props.headerHeight ? props.headerHeight : 0;
    return props.items.length * props.itemHeight + headerHeight;
});
</script>
