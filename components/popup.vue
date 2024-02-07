<template>
    <view class="kiwi-popup"
          :class="[transitionClass,{'header-fill':headerFill,bottom}]"
          :style="{zIndex,bottom,position}"
          v-if="show"
          @touchmove.stop>
        <view class="bg"
              :class="{blur}"
              :style="{background}"
              @touchstart.stop="close"></view>
        <view class="content"
              :class="{flex,center}"
              ref="popupContent">
            <slot></slot>
        </view>
    </view>
</template>

<script lang="ts">
    import { Vue, Component, Prop, Watch } from "vue-facing-decorator";

    @Component({
        name: "KiwiPopup"
    })
    export default class KiwiPopup extends Vue {
        @Prop({ type: Boolean, default: undefined })
        public value?: boolean;

        @Prop({ type: Boolean })
        public center?: boolean;

        @Prop({ default: 999 })
        public zIndex!: string | number;

        @Prop({ type: Boolean })
        public disTransition?: boolean;

        @Prop({ default: "rgba(0,0,0,.6)" })
        public background!: string;

        @Prop({ default: "fade" })
        public transition!: string;

        @Prop({ default: "" })
        public flex!: string;

        @Prop({ type: Boolean, default: true })
        public closeOnClickOverlay?: boolean;

        @Prop({ type: Boolean })
        public headerFill?: boolean;

        @Prop({ type: String })
        public bottom!: string;

        @Prop({ type: String, default: "fixed" })
        public position!: string;

        @Prop({ type: Boolean })
        public blur?: boolean;

        public show = false;
        public transitionClass = "";

        private enterTimer = 0;
        private enterToTimer = 0;
        private leaveTimer = 0;
        private leaveToTimer = 0;

        @Watch("value")
        async onVisibleChanged(val: boolean) {
            if(val) {
                if(this.disTransition) {
                    this.show = true;
                } else {
                    clearTimeout(this.leaveTimer);
                    clearTimeout(this.leaveToTimer);
                    this.show = true;
                    this.transitionClass = `${this.transition}-enter`;
                    this.enterTimer = setTimeout(() => {
                        this.transitionClass = `${this.transition}-enter-to`;
                    }, 20);
                    this.enterToTimer = setTimeout(() => {
                        this.transitionClass = "";
                    }, 350);
                }
            } else {
                if(this.disTransition) {
                    this.show = false;
                } else {
                    clearTimeout(this.enterTimer);
                    clearTimeout(this.enterToTimer);
                    this.transitionClass = `${this.transition}-leave`;
                    this.leaveTimer = setTimeout(() => {
                        this.transitionClass = `${this.transition}-leave-to`;
                    }, 20);
                    this.leaveToTimer = setTimeout(() => {
                        this.transitionClass = "";
                        this.show = false;
                    }, 350);
                }
            }
        }

        public close(event: Event) {
            this.$emit("maskTrigger", event);
            if(this.closeOnClickOverlay) {
                this.$emit("input", false);
            }
        }
    }
</script>

<style lang="less" scoped>
    .kiwi-popup {
        position: fixed;
        top: 0;
        left: 0;
        width: 100%;
        height: 100%;
        &.header-fill {
            //#ifdef H5
            top: calc(44PX + env(safe-area-inset-top));
            //#endif
        }
        &.bottom {
            top: auto;
        }
        .bg {
            z-index: 1;
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            transition: opacity .3s;
            &.blur {
                backdrop-filter: blur(10px);
            }
        }
        .content {
            z-index: 2;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            &.flex {
                display: flex;
                flex-direction: column;
            }
            &.center {
                .align(center);
            }
        }
    }
    .fade-enter-active,
    .fade-leave-active {
        opacity: 1;
        transition: opacity .3s;
        .bg {
            opacity: 1;
        }
    }
    .fade-enter,
    .fade-leave-to {
        opacity: 1;
        .bg {
            opacity: 0;
        }
    }
</style>
