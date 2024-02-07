<template>
    <view class="kiwi-loading"
          :class="[{fix},{'align-center':center}]"
          :style="{background,display:center?'flex':'inline-block'}"
          v-if="modeVisible">
        <view class="custom"
              v-if="$slots.default">
            <slot></slot>
        </view>
        <template v-else>
            <view class="spin"
                  :class="size"
                  v-if="type==='spin'">
                <view class="part"
                      v-for="i in 12"
                      :key="i"
                      :style="{backgroundColor:color}"></view>
            </view>
            <view class="point"
                  :class="size"
                  v-else-if="type==='point'">
                <view class="part"
                      :style="{color}"></view>
            </view>
        </template>
    </view>
</template>

<script lang="ts">
    import { Vue, Component, Prop } from "vue-facing-decorator";

    @Component({
        name: "KiwiLoading"
    })
    export default class KiwiLoading extends Vue {
        @Prop({ type: Boolean })
        public fix?: boolean;

        @Prop({ default: "default" })
        public size?: "default" | "large" | "small";

        @Prop({ default: "#000" })
        public color?: string;

        @Prop({ type: Boolean })
        public fullScreen?: boolean;

        @Prop({ default: "transparent" })
        public background?: string;

        @Prop({ type: Boolean })
        public center?: boolean;

        @Prop({ default: "spin" })
        public type!: "spin" | "point";

        public visible = false;

        public get modeVisible() {
            return this.fullScreen ? this.visible : true;
        }
    }
</script>

<style lang="less" scoped>
    .kiwi-loading {
        display: inline-block;
        vertical-align: middle;
        text-align: center;
        &.align-center {
            .align(center);
        }
        .spin {
            position: relative;
            width: 24PX;
            height: 24PX;
            &.large {
                width: 30PX;
                height: 30PX;
            }
            &.small {
                width: 18PX;
                height: 18PX;
            }
            .part {
                .absolute(cm);
                width: 10%;
                height: 25%;
                border-radius: 30%;
                opacity: 0;
                animation: fade 1s linear infinite;
                &:nth-child(1) {
                    transform: rotate(0deg) translate(0, -142%);
                    animation-delay: 0s;
                }
                &:nth-child(2) {
                    transform: rotate(30deg) translate(0, -142%);
                    animation-delay: -.9167s;
                }
                &:nth-child(3) {
                    transform: rotate(60deg) translate(0, -142%);
                    animation-delay: -.833s;
                }
                &:nth-child(4) {
                    transform: rotate(90deg) translate(0, -142%);
                    animation-delay: -.75s;
                }
                &:nth-child(5) {
                    transform: rotate(120deg) translate(0, -142%);
                    animation-delay: -.667s;
                }
                &:nth-child(6) {
                    transform: rotate(150deg) translate(0, -142%);
                    animation-delay: -.5833s;
                }
                &:nth-child(7) {
                    transform: rotate(180deg) translate(0, -142%);
                    animation-delay: -.5s;
                }
                &:nth-child(8) {
                    transform: rotate(210deg) translate(0, -142%);
                    animation-delay: -.41667s;
                }
                &:nth-child(9) {
                    transform: rotate(240deg) translate(0, -142%);
                    animation-delay: -.333s;
                }
                &:nth-child(10) {
                    transform: rotate(270deg) translate(0, -142%);
                    animation-delay: -.25s;
                }
                &:nth-child(11) {
                    transform: rotate(300deg) translate(0, -142%);
                    animation-delay: -.1667s;
                }
                &:nth-child(12) {
                    transform: rotate(330deg) translate(0, -142%);
                    animation-delay: -.0833s;
                }
            }
            @keyframes fade {
                from { opacity: 1; }
                to { opacity: .25; }
            }
        }

        .point {
            position: relative;
            &.default {
                width: 42PX;
                height: 8PX;
                .part {
                    &,
                    &::before,
                    &::after {
                        width: 8PX;
                        height: 8PX;
                    }
                    &::before {
                        left: -17PX;
                    }
                    &::after {
                        left: 17PX;
                    }
                }
            }
            &.small {
                width: 22PX;
                height: 4PX;
                .part {
                    &,
                    &::before,
                    &::after {
                        width: 4PX;
                        height: 4PX;
                    }
                    &::before {
                        left: -9PX;
                    }
                    &::after {
                        left: 9PX;
                    }
                }
            }
            &.large {
                width: 62PX;
                height: 12PX;
                .part {
                    &,
                    &::before,
                    &::after {
                        width: 12PX;
                        height: 12PX;
                    }
                    &::before {
                        left: -25PX;
                    }
                    &::after {
                        left: 25PX;
                    }
                }
            }
            .part,
            .part::before,
            .part::after {
                border-radius: 50%;
                background-color: currentColor;
            }
            .part {
                .absolute(c);
                animation: pointm 1s ease-in-out infinite;
                &::before,
                &::after {
                    content: "";
                    position: absolute;
                    top: 0;
                }
                &::before {
                    animation: pointg 1s ease-in-out infinite;
                }
                &::after {
                    animation: pointd 1s ease-in-out infinite;
                }
                @keyframes pointg {
                    0% { opacity: .2; }
                    25% { opacity: 1; }
                    50% { opacity: .2; }
                    75% { opacity: .2; }
                    100% { opacity: .2; }
                }
                @keyframes pointm {
                    0% { opacity: .2; }
                    25% { opacity: .2; }
                    50% { opacity: 1; }
                    75% { opacity: .2; }
                    100% { opacity: .2; }
                }
                @keyframes pointd {
                    0% { opacity: .2; }
                    25% { opacity: .2; }
                    50% { opacity: .2; }
                    75% { opacity: 1; }
                    100% { opacity: .2; }
                }
            }
        }

        .custom {
            position: absolute;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
        }
        &.fix {
            z-index: 10;
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            .spin {
                .custom();
            }
        }
    }
</style>
