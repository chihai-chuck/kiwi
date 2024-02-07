<template>
    <div class="kiwi-load">
        <text v-if="end">没有更多了</text>
        <loading type="point"
                 color="#666"
                 v-else></loading>
    </div>
</template>

<script lang="ts">
    import { Vue, Component, Prop } from "vue-facing-decorator";
    import loading from "./loading.vue";

    @Component({
        name: "KiwiLoad",
        components: {
            loading
        }
    })
    export default class KiwiLoad extends Vue {
        /** 是否尾页 */
        @Prop({ type: Boolean, default: false }) end!: boolean;
    }
</script>

<style lang="less" scoped>
    .kiwi-load {
        .align(center);
        flex-shrink: 0;
        width: 100%;
        height: 100px;
        > text {
            font-size: 26px;
            color: #999;
        }
    }
</style>
