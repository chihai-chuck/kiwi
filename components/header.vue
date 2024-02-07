<template>
    <view class="kiwi-header"
          :class="{line, spacing, transparent}"
          :style="{color, position, paddingTop: statusBarHeight+'px'}">
        <view class="container">
            <view class="control">
                <view class="home icon-kiwi-home"
                      @click="homeHandler"
                      v-if="home">{{homeText}}</view>
                <view class="back icon-kiwi-arrow-left"
                      @click="backHandler"
                      v-else-if="back">{{backText}}</view>
                <view class="icons left">
                    <view class="icon"
                          v-for="(icon,i) in iconSub"
                          :key="i"
                          :class="icon.icon"
                          @click="icon.active"
                          :style="{fontSize:icon.size}">{{icon.caption}}</view>
                </view>
                <view class="fill"></view>
                <view class="icons right">
                    <view class="icon"
                          v-for="(icon,i) in icons"
                          :key="i"
                          :class="icon.icon"
                          @click="icon.active"
                          :style="{fontSize:icon.size}">{{icon.caption}}</view>
                </view>
            </view>
            <view class="content">
                <slot></slot>
            </view>
        </view>
    </view>
</template>

<script lang="ts">
    import { Vue, Component, Prop } from "vue-facing-decorator";

    import { selectorCalc } from "@pecasha/uniapp-helpers";

    interface IconItem {
        icon: string;
        caption?: string;
        size?: string;
        active?: Function;
    }

    @Component({
        name: "KiwiHeader"
    })
    export default class KiwiHeader extends Vue {
        /** 是否显示边线 */
        @Prop()
        public line?: boolean;

        /** 是否显示主页按钮（主页和返回按钮不可同时显示，主页优先级更高） */
        @Prop()
        public home?: boolean;

        /** 主页按钮显示的文本 */
        @Prop()
        public homeText?: string;

        /** 是否显示返回按钮，默认true */
        @Prop({ default: true })
        public back?: boolean;

        /** 返回按钮显示的文本 */
        @Prop()
        public backText?: string;

        /** 背景全透明 */
        @Prop()
        public transparent?: boolean;

        /** 字体和图标颜色 */
        @Prop({ default: "#333" })
        public color?: string;

        /** 自定义position，有些场景可能需要改为fixed或者absolute */
        @Prop({ default: "sticky" })
        public position?: string;

        /** 右侧图标 */
        @Prop({ default: Array })
        public icons?: IconItem[];

        /** 左侧图标 */
        @Prop({ default: Array })
        public iconSub?: IconItem[];

        /** 底部添加间距 */
        @Prop()
        public spacing?: boolean;

        /** 状态栏高度 */
        @Prop({ default: 0 })
        public statusBarHeight?: number;

        /** header的尺寸 */
        public headerSize = {} as UniNamespace.NodeInfo;

        /** 获取header的尺寸，可以用来获取高度等信息，单位都px */
        public getHeaderSize() {
            return selectorCalc(".kiwi-header", {
                size: true
            }, this);
        }

        async mounted() {
            await this.$nextTick();
            this.headerSize = await this.getHeaderSize();
            this.$emit("height", this.headerSize.height);
        }

        public backHandler() {
            uni.navigateBack({});
        }

        public homeHandler() {
            uni.switchTab({
                url: "/pages/home"
            });
        }
    }
</script>

<style lang="less" scoped>
    .kiwi-header {
        z-index: 990;
        top: 0;
        left: 0;
        width: 100%;
        background-color: #fff;
        &.line {
            border-bottom: 1PX solid #eee;
        }
        &.transparent {
            background-color: transparent;
        }
        &.spacing {
            margin-bottom: 20px;
        }
        .container {
            position: relative;
            width: 100%;
            height: 90px;
            .control,
            .content {
                width: 100%;
                height: 100%;
            }
            .content {
                .align(center);
                font-size: 30px;
            }
            .control {
                display: flex;
                z-index: 1;
                position: absolute;
                top: 0;
                left: 0;
                .fill {
                    flex: 1;
                    height: 100%;
                }
                .back,
                .home {
                    .align(v-center);
                    .align(left);
                    min-width: 90px;
                    height: 90px;
                    padding-left: 34px;
                    padding-right: 20px;
                    font-size: 28px;
                    &::before {
                        margin-right: 5px;
                        font-size: 34px;
                    }
                }
                .icons {
                    .icon {
                        width: 90px;
                        height: 90px;
                        font-size: 38px;
                    }
                }
            }
        }
    }
</style>
