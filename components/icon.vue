<template>
    <view class="kiwi-icon monochrome"
          :class="[name,{center},{margin:$slots.default}]"
          :style="{
              fontSize: size,
              color: color
          }"
          @click="$emit('click',$event)"
          v-if="monochrome"><slot></slot></view>
    <view class="kiwi-icon multiple"
          :class="{margin:$slots.default}"
          @click="$emit('click',$event)"
          v-else>
        <!-- #ifdef H5 -->
        <svg aria-hidden="true"
             :style="{fontSize:size}">
            <use :xlink:href="`#${name}`"></use>
        </svg>
        <!-- #endif -->
        <!-- #ifndef H5 -->
        <view class="icon-color"
              :class="name"
              :style="{width:size,height:size}"></view>
        <!-- #endif -->
        <slot></slot>
    </view>
</template>

<script lang="ts">
    import { Vue, Component, Prop } from "vue-facing-decorator";

    @Component({
        name: "KiwiIcon"
    })
    export default class KiwiIcon extends Vue {
        /** 图标名称 */
        @Prop({ type: String, required: true })
        public name!: string;

        /** 图标大小，需带单位传入 */
        @Prop()
        public size?: string;

        /** 图标颜色（仅在单色图标下生效） */
        @Prop()
        public color?: string;

        /** 单色模式，将不会使用svg显示图标，这时可以传入一个slot */
        @Prop({ type: Boolean, default: undefined })
        public monochrome?: boolean;

        /** 是否将图标内容居中（这可能会导致图标与周围文本无法在同一行显示） */
        @Prop({ type: Boolean, default: undefined })
        public center?: boolean;
    }
</script>

<style lang="less" scoped>
    .kiwi-icon {
        &.multiple {
            .align(center, inline-flex);
            //#ifdef H5
            &.margin > svg {
                margin-right: 6px;
            }
            > svg {
                display: inline-block;
                width: 1em;
                height: 1em;
                overflow: hidden;
            }
            //#endif
        }
        &.monochrome {
            display: inline-block;
            &.center {
                .align(center);
            }
            &.margin::before {
                margin-right: 6px;
            }
        }
    }
</style>
<style lang="less">
    kiwi-icon {
        .align(v-center);
        line-height: 1;
    }
</style>
