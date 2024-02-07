<template>
    <view class="kiwi-swipeout">
        <view class="kiwi-swipeout-container"
              :style="{transform:'translate3d('+position.pageX+'px,0px,0px)'}"
              @transitionend="openStatusChange">
            <view class="kiwi-swipeout-box"
                  @touchstart="handlerTouchstart"
                  @touchmove.prevent="handlerTouchmove"
                  @touchend.capture="handlerTouchend">
                <view class="kiwi-swipeout-content"
                      :class="{lock:openStatus}"><slot></slot></view>
            </view>
            <view class="kiwi-swipeout-left"
                  @touchend.stop="reset">
                <view class="kiwi-swipeout-left-content"><slot name="left"></slot></view>
            </view>
            <view class="kiwi-swipeout-right"
                  @touchend.stop="reset">
                <view class="kiwi-swipeout-right-content"><slot name="right"></slot></view>
            </view>
        </view>
    </view>
</template>

<script lang="ts">
    import { Vue, Component, Prop, Watch } from "vue-facing-decorator";

    interface Rect {
        box: UniNamespace.NodeInfo;
        left: UniNamespace.NodeInfo;
        right: UniNamespace.NodeInfo
    }

    @Component({
        name: "KiwiSwipeout"
    })
    export default class KiwiSwipeout extends Vue {
        /** 是否禁用 */
        @Prop({ type: Boolean, default: false })
        public disabled?: boolean;

        private rect: Rect = {
            box: {},
            left: {},
            right: {}
        }
        private tStart = {
            pageX: 0,
            pageY: 0
        }
        private limitMove = {
            left: 0,
            right: 0
        }
        public position = {
            pageX: 0,
            pageY: 0
        }
        private moveLock = "none" // move的锁最好不要跟end的锁共用一个，宁愿多写一些重复代码，避免因冲突引起问题难以排查
        private open = "none";
        public openStatus = false;

        private get slots() {
            // #ifdef MP-ALIPAY
            return {
                default: !!this.rect.box.height,
                left: !!this.rect.left.width,
                right: !!this.rect.right.width
            }
            // #endif
            // #ifndef MP-ALIPAY
            return this.$slots;
            // #endif
        }

        @Watch("open")
        @Watch("moveLock")
        private swipperLock(type: string) {
            if (type === "none") {
                this.getLimitMove();
            } else if (type === "left") {
                this.limitMove.right = 0;
            } else if (type === "right") {
                this.limitMove.left = 0;
            }
        }

        mounted() {
            this.getRect();
        }

        private loop() {}

        public handlerTouchstart(event: TouchEvent) {
            if (this.disabled || (!this.slots.left && !this.slots.right)) {
                return;
            }
            const touches = event.touches ? event.touches[0] : void 0;
            if (touches) {
                for (let key of Object.keys(this.tStart) as ("pageX"|"pageY")[]) {
                    if (touches[key]) {
                        this.tStart[key] = touches[key];
                    }
                }
            }
        }
        public handlerTouchmove(event: TouchEvent) {
            if (this.disabled || (!this.slots.left && !this.slots.right)) {
                return;
            }
            const touches = event.touches ? event.touches[0] : void 0;
            if (touches) {
                const direction = this.swipeDirection(
                    this.tStart.pageX,
                    touches.pageX,
                    this.tStart.pageY,
                    touches.pageY
                );
                if (direction === 'left' || direction === "right") {
                    this.swipper(touches, direction);
                }
            }
        }
        public handlerTouchend(event: TouchEvent) {
            if (this.disabled || (!this.slots.left && !this.slots.right)) {
                return;
            }
            this.moveLock = "none";
            const touches = event.changedTouches ? event.changedTouches[0] : void 0;
            if (touches) {
                const direction = this.swipeDirection(
                    this.tStart.pageX,
                    touches.pageX,
                    this.tStart.pageY,
                    touches.pageY
                );
                const spacing = {
                    pageX: touches.pageX - this.tStart.pageX,
                    pageY: touches.pageY - this.tStart.pageY
                }
                const reset = () => {
                    spacing.pageX = 0;
                    this.open = "none";
                }
                if (Math.abs(spacing.pageX) >= 40 && direction === "left" && this.slots.right) {
                    if (this.open === "left") {
                        reset();
                    } else {
                        spacing.pageX = spacing.pageX < 0 ? -this.limitMove.right : this.limitMove.right;
                        this.open = "right";
                    }
                } else if (Math.abs(spacing.pageX) >= 40 && direction === "right" && this.slots.left) {
                    if (this.open === "right") {
                        reset();
                    } else {
                        spacing.pageX = spacing.pageX < 0 ? -this.limitMove.left : this.limitMove.left;
                        this.open = "left";
                    }
                } else {
                    reset();
                    // setTimeout(reset, 100); // 此处延时为了延缓slot内元素事件的触发时间，以免还原点击与slot内元素的点击事件发生冲突
                }
                this.position = spacing;
            }
        }

        private async getRect() {
            this.rect.box = await this.calcRect(".kiwi-swipeout-box") || {};
            this.rect.left = await this.calcRect(".kiwi-swipeout-left-content") || {};
            this.rect.right = await this.calcRect(".kiwi-swipeout-right-content") || {};

            this.getLimitMove();
        }

        public reset() {
            this.position.pageX = 0;
            this.open = "none";
        }

        private getLimitMove() {
            this.limitMove.left = this.slots.left ? this.rect.left.width || 0 : 0;
            this.limitMove.right = this.slots.right ? this.rect.right.width || 0 : 0;
        }

        private calcRect(selector: string): Promise<UniApp.NodeInfo> {
            return new Promise<any>(resolve => {
                // #ifndef MP-WEIXIN
                uni.createSelectorQuery().select(selector).boundingClientRect(resolve).exec();
                // #endif
                // #ifdef MP-WEIXIN
                (this as any).createSelectorQuery().select(selector).boundingClientRect(resolve).exec();
                // #endif
            });
        }

        private swipeDirection(x1: number, x2: number, y1: number, y2: number) {
            return Math.abs(x1 - x2) >= Math.abs(y1 - y2) ?
                (x1 - x2 > 0 ? 'left' : 'right') :
                (y1 - y2 > 0 ? 'up' : 'down');
        }

        private swipper(touches: Touch, direction: "left" | "right") {
            const spacing = {
                pageX: touches.pageX - this.tStart.pageX,
                pageY: touches.pageY - this.tStart.pageY
            }

            if (direction === "left") {
                this.moveLock = "right";
                if (this.limitMove.right < Math.abs(spacing.pageX)) {
                    spacing.pageX = -this.limitMove.right;
                }
            } else if (direction === "right") {
                this.moveLock = "left";
                if (this.limitMove.left < Math.abs(spacing.pageX)) {
                    spacing.pageX = this.limitMove.left;
                }
            }
            this.position = spacing;
        }

        public openStatusChange() {
            this.openStatus = this.open !== "none";
        }
    }
</script>

<style lang="less" scoped>
    .kiwi-swipeout {
        position: relative;
        width: 100%;
        overflow: hidden;
        &-container {
            width: 100%;
            transition: transform .2s ease;
            cursor: grab;
        }
        &-content,
        &-box {
            width: 100%;
        }
        &-content.lock {
            pointer-events: none;
        }
        &-left,
        &-right {
            position: absolute;
            top: 0;
            width: 100%;
            height: 100%;
            &-content {
                display: inline-flex;
                height: 100%;
            }
        }
        &-left {
            left: -100%;
            text-align: right;
        }
        &-right {
            right: -100%;
        }
    }
</style>
