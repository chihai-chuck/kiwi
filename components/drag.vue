<template>
    <view class="kiwi-drag"
          :class="{transition:dock&&!isMove}"
          @touchstart="$emit('touchstart',$event)"
          @touchmove.stop.prevent="touchmove"
          @touchend="touchend"
          @click.stop.prevent="$emit('click',$event)"
          :style="{top:`${top}px`,left:`${left}px`,width:`${_width}px`,height:`${_height}px`}"><slot></slot></view>
</template>

<script lang="ts">
    import { Vue, Component, Prop, Emit } from "vue-facing-decorator";

    @Component({
        name: "KiwiDrag",
        emits: ["touchend"]
    })
    export default class KiwiDrag extends Vue {
        @Prop({ type: Boolean, default: false })
        public dock!: boolean;

        @Prop({ type: Boolean, default: false })
        public existTabBar!: boolean;

        @Prop({ type: Number, default: 0 })
        public width!: number;

        @Prop({ type: Number, default: 0 })
        public height!: number;

        private deviceInfo = uni.getSystemInfoSync();

        public top = 0;
        public left = 0;
        public _width = 0;
        public _height = 0;
        private offsetWidth = 0;
        private offsetHeight = 0;
        private windowWidth = 0;
        private windowHeight = 0;
        public isMove = true;
        private edge = 10;

        async mounted() {
            await this.$nextTick();
            // const _w = (this.deviceInfo.safeArea?.width || 0) - (this.deviceInfo.safeArea?.left || 0);
            // const _h = (this.deviceInfo.safeArea?.height || 0) - (this.deviceInfo.safeArea?.top || 0);
            this.windowWidth = this.deviceInfo.windowWidth;
            this.windowHeight = this.deviceInfo.windowHeight;

            // #ifdef APP-PLUS
            if(this.existTabBar) {
                this.windowHeight -= 50;
            }
            // #endif
            if(this.deviceInfo.windowTop) {
                this.windowHeight += this.deviceInfo.windowTop;
            }

            if(this.width && this.height) {
                this._width = uni.upx2px(this.width);
                this._height = uni.upx2px(this.height);
            } else {
                await new Promise<void>(resolve => {
                    uni.createSelectorQuery().in(this).select(".kiwi-drag").boundingClientRect(data => {
                        const _data = data as UniNamespace.NodeInfo;
                        this._width = _data.width || 0;
                        this._height = _data.height || 0;
                        resolve();
                    }).exec();
                });
            }
            this.offsetWidth = this._width / 2;
            this.offsetHeight = this._height / 2;
            this.top = this.windowHeight - this._height - this.edge;
            this.left = this.windowWidth - this._width - this.edge;
        }

        public touchmove(event: any) {
            // 单指触摸
            if(event.touches.length !== 1) {
                return false;
            }

            this.isMove = true;

            let clientX = event.touches[0].clientX - this.offsetWidth;
            // #ifdef H5
            clientX += this._width;
            //#endif
            const edgeRight = this.windowWidth - this._width - this.edge;

            let clientY = event.touches[0].clientY - this.offsetHeight;
            // #ifdef H5
            clientY += this._height;
            // #endif
            const edgeBottom = this.windowHeight - this._height - this.edge;

            // 上下触及边界
            if(clientY < this.edge) {
                this.top = this.edge;
            } else if(clientY > edgeBottom) {
                this.top = edgeBottom;
            } else {
                this.top = clientY
            }

            // 左右触及边界
            if(clientX < this.edge) {
                this.left = this.edge;
            } else if(clientX > edgeRight) {
                this.left = edgeRight;
            } else {
                this.left = clientX;
            }
        }

        @Emit("touchend")
        public touchend() {
            if(this.dock) {
                if(this.left < this.windowWidth / 2 - this.offsetWidth) {
                    this.left = this.edge;
                } else {
                    this.left = this.windowWidth - this._width - this.edge;
                }
            }

            this.isMove = false;
        }
    }
</script>

<style lang="less" scoped>
    .kiwi-drag {
        .align(center);
        z-index: 500;
        position: fixed;
        &.transition {
            transition: left .3s ease,top .3s ease;
        }
    }
</style>
