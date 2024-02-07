<template>
    <view class="kiwi-cell-item"
          :class="{spacing,'not-line':notSplitLine,arrow:next||nextIcon}"
          :style="{background}"
          @click="active">
        <view class="wrapper"
              v-if="icon||title||$slots.title||$slots.prefix">
            <slot name="prefix"
                  v-if="$slots.prefix"></slot>
            <view :class="['icon',icon,iconClass]"
                  :style="{color:iconColor}"
                  v-else-if="icon"></view>
            <text class="title"
                  :class="{important,fixed:_titleFixed!=='auto'}"
                  :style="{fontFamily:font,color,width:_titleFixed}"
                  v-if="title||$slots.title"><slot name="title" v-if="$slots.title"></slot><template v-else>{{title}}</template></text>
        </view>
<!--        <slot name="custom"></slot>-->
        <text class="content"
              :class="{flex:textFlex,right:_textRight}"
              :style="{color:textColor}"><slot></slot></text>
    </view>
</template>

<script lang="ts">
    import { Vue, Component, Prop } from "vue-facing-decorator";

    @Component({
        name: "KiwiCellItem",
        emits: ["click"]
    })
    export default class KiwiCellItem extends Vue {
        /* 是否显示右边icon，默认false */
        @Prop({ type: Boolean })
        public nextIcon?: boolean;

        /* 在点击时触发路由跳转，并且会默认显示nextIcon */
        @Prop()
        public next?: string;

        /* 列表图标 */
        @Prop({ default: "" })
        public icon!: string;

        /* 列表图标颜色 */
        @Prop({ default: "" })
        public iconColor!: string;

        /* 列表图标className */
        @Prop({ default: "" })
        public iconClass!: string;

        /* 列表标题 */
        @Prop({ default: "" })
        public title!: string;

        /* 列表标题文字颜色 */
        @Prop({ default: "" })
        public color!: string;

        /* 列表文本内容文字颜色 */
        @Prop({ default: "" })
        public textColor!: string;

        /* 是否在底部增加20px的margin间距 */
        @Prop({ type: Boolean })
        public spacing?: boolean;

        /* 是否显示分割线 */
        @Prop({ type: Boolean })
        public notSplitLine?: boolean;

        /* 列表自定义背景 */
        @Prop({ default: "" })
        public background!: string;

        /* 是否在标题前加星号标识 */
        @Prop()
        public important?: boolean;

        /* 固定列表标题宽度 */
        @Prop({ default: "auto" })
        public titleFixed!: string;

        /* 列表文本内容区域是否使用flex布局 */
        @Prop({ type: Boolean })
        public textFlex?: boolean;

        /* 列表文本内容区域是否右对齐 */
        @Prop({ type: Boolean, default: false })
        public textRight!: boolean;

        /** 自定义字体 */
        @Prop({ default: "PingFang SC, Microsoft YaHei, iconfont, sans-serif" })
        public font!: string;

        public _titleFixed = this.titleFixed;
        public _textRight = this.textRight;

        async created() {
            const $parent = (<any>this.$parent);
            this._titleFixed = $parent.titleFixed || this.titleFixed;
            this._textRight = $parent.textRight || this.textRight;
        }

        public active() {
            if(this.next) {
                this.$navigate.to(this.next);
            } else {
                this.$emit("click");
            }
        }
    }
</script>

<style lang="less" scoped>
    .kiwi-cell-item {
        .align(v-center);
        position: relative;
        width: 100%;
        min-height: 100px;
        margin-top: 1PX;
        padding: 0 30px;
        background-color: rgba(255,255,255,.6);
        border: 1PX solid #fff;
        font-size: 30px;
        &.not-line,
        &.spacing {
            &::before {
                border: 0;
            }
        }
        &.arrow {
            padding-right: 60px;
            &::before {
                content: "\e71a";
                position: absolute;
                right: 30px;
                font-family: iconfont, sans-serif;
                font-size: 20px;
                color: #999;
                pointer-events: none;
            }
        }
        &.spacing {
            margin-bottom: 20px;
        }
        .wrapper {
            .align(v-center);
            margin-right: 30px;
            .icon {
                margin-right: 20px;
                font-size: 40px;
                & + .title.important {
                    padding-left: 14px;
                    &::before {
                        left: 0;
                    }
                }
            }
            .title {
                font-size: 28px;
                color: #444;
                &.important {
                    position: relative;
                    &::before {
                        .absolute(m);
                        left: -14px;
                        content: "*";
                        color: #f05a50;
                    }
                }
                &.fixed {
                    display: block;
                }
            }
        }
        .content {
            flex: 1;
            width: 0;
            word-break: break-all;
            font-size: 28px;
            color: #999;
            &:not(.left) {
                .align(right);
            }
            &.flex {
                display: flex;
            }
            :deep(.long) {
                display: block;
                width: 100%;
            }
            :deep(.right) {
                text-align: right;
            }
        }
    }
</style>
