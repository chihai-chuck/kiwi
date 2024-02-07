<template>
    <view class="kiwi-button"
          :class="{long, disabled, center, margin:!!margin, loading:isLoading}"
          :style="{background, border, width, height, margin, borderRadius, flex:cssFlex}"
          :disabled="disabled"
          @click.stop="handleClick">
        <loading size="small"
                 :color="color"
                 v-if="isLoading"></loading>
        <view class="icon"
              :class="[icon, {loading:isLoading}]"><text :class="{bold}" :style="{color,fontFamily:font,fontSize:size}"><slot></slot></text></view>
    </view>
</template>

<script lang="ts">
    import { Vue, Component, Prop, Watch } from "vue-facing-decorator";
    import loading from "./loading.vue";

    @Component({
        name: "KiwiButton",
        emits: ["click"],
        components: {
            loading
        }
    })
    export default class KiwiButton extends Vue {
        /* 按钮宽度，默认自适应内容（自定义需要带单位） */
        @Prop()
        public width?: string;

        /* 按钮高度，默认80rpx（自定义需要带单位） */
        @Prop()
        public height?: string;

        /* 开启loading状态动画（开启loading后按钮也会被禁用） */
        @Prop()
        public loading?: boolean;

        /* 异步任务按钮（开启后在点击事件触发时会自动开启loading，然后在触发的click事件中会传递一个回调函数，调用这个回调函数后会关闭自动loading状态，注意开启后click事件不再传递event对象） */
        @Prop()
        public async?: boolean;

        /* 禁用按钮，点击事件失效 */
        @Prop()
        public disabled?: boolean;

        /* 自定义背景 */
        @Prop()
        public background?: string;

        /* 自定义颜色（包含loading图标颜色） */
        @Prop({ default: "#fff" })
        public color?: string;

        /** 按钮文字是否加粗 */
        @Prop({ default: false })
        public bold?: boolean;

        /* 按钮图标 */
        @Prop({ default: ""})
        public icon?: string;

        /* 全宽按钮，按钮会达到父级元素的100%宽度 */
        @Prop()
        public long?: boolean;

        /* 按钮相对于父级元素居中（不建议使用这个去居中按钮，好的方式应当通过改变父级元素样式去居中内容） */
        @Prop()
        public center?: boolean;

        /* 自定义按钮margin样式值，例如center只能居中但是不能设置顶部或底部边距，就可以改用margin实现（设置了margin后按钮display会变为flex） */
        @Prop({ default: "" })
        public margin?: string;

        /* 按钮圆角，true默认高度80rpx，字符串自定义（如果改变了按钮height并且需要圆角，需要自定义） */
        @Prop({ default: false })
        public radius?: string | boolean;

        /* 自定义按钮的边框样式 */
        @Prop({ default: "" })
        public border?: string;

        /* 自定义按钮的字体大小 */
        @Prop({ default: "30rpx" })
        public size?: string;

        /* 设置button的flex，true默认为1，字符串自定义（开启了long时不建议同时设置flex） */
        @Prop({ default: false })
        public flex?: string | boolean;

        /** 字体名称 */
        @Prop({ default: "PingFang SC, Microsoft YaHei, iconfont, sans-serif" })
        public font!: string;

        public isLoading = false;

        @Watch("loading")
        onLoadingChanged() {
            this.isLoading = !!this.loading;
        }

        created() {
            this.isLoading = !!this.loading;
        }

        public get borderRadius() {
            if(typeof this.radius === "boolean" && this.radius) {
                return "80rpx";
            }
            return this.radius as string;
        }

        public get cssFlex() {
            if(typeof this.flex === "boolean" && this.flex) {
                return "1";
            }
            return this.flex as string;
        }

        public handleClick(event: Event) {
            if(this.disabled || this.isLoading) {
                return;
            }
            if(this.async) {
                this.isLoading = true;
            }
            this.$emit("click", this.async ? () => {
                this.isLoading = false;
            } : event);
        }
    }
</script>

<style lang="less" scoped>
    .kiwi-button {
        .align(center, inline-flex);
        position: relative;
        height: 80px;
        padding: 0 20px;
        vertical-align: middle;
        border-radius: 10px;
        background-color: #f3344e;
        &::before {
            content: "";
            z-index: 10;
            position: absolute;
            top: -1px;
            left: -1px;
            right: -1px;
            bottom: -1px;
            pointer-events: none;
        }
        &.loading::before {
            background-color: #fff;
            opacity: .35;
            transition: opacity .2s ease;
        }
        &.long {
            display: flex !important;
            width: 100%;
        }
        &.disabled {
            background: #d5d5d5 !important;
            .icon text {
                color: #666 !important;
            }
        }
        &.center,
        &.margin {
            display: flex !important;
        }
        &.center {
            margin: 0 auto;
        }
        &.radius {
            border-radius: 50%;
        }
        .icon {
            vertical-align: middle;
            &.loading {
                margin-left: 10px;
            }
            &::before {
                margin-right: 5px;
            }
            text.bold {
                font-weight: bold;
            }
        }
    }
</style>
