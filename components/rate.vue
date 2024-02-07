<template>
    <view class="kiwi-rate">
        <view class="item"
              v-for="(n,i) in 5"
              :key="n">
            <view class="star"
                  :style="{width:i<count?'100%':i===count?`${remainder}%`:'0'}"></view>
        </view>
    </view>
</template>

<script lang="ts">
    import { Vue, Component, Prop } from "vue-facing-decorator";

    @Component({
        name: "Rate"
    })
    export default class Rate extends Vue {
        @Prop({ type: Number, required: true }) progress!: number;

        private get score() {
            return this.progress / 20;
        }

        public get remainder() {
            return this.score.toFixed(2).split(".")[1];
        }

        public get count() {
            return Math.floor(this.score);
        }
    }
</script>

<style lang="less" scoped>
    .kiwi-rate {
        display: flex;
        width: 128px;
        height: 23px;
        .item {
            width: 24px;
            height: 23px;
            background: url(data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAABgAAAAXBAMAAADjD5IeAAAALVBMVEXwwAcAAADzzDbwwAfwwAfwwAfwwAf22GXxxBbwwAfwwAf101LwwAfwwAfwwAftw1B0AAAAD3RSTlMzADMvGBwRMzMmCjMtKxQPWmGdAAAAo0lEQVQY00XQoQrCUBSH8Q+3wRQUD7guS4JlFvPWDTOabYLFonlPYLGYTILVshcwi8kXsPoQ3p17d/3K4ce/HaRpPddjAX8ksPQYwsyjjIk8sorA4boihdPG4JYBd0zBmRfQ2dHUx1TVB0VAzrTejxUhCXE6QSuQElcoyKhFYSB5OzS4WPQM/DJQlBaR4mjRVWQWgQJ4bwGHp8jX4fMQ08I86AdklRr3XkHcKwAAAABJRU5ErkJggg==) no-repeat 0 0;
            background-size: 24px 23px;
            &:not(:last-child) {
                margin-right: 2px;
            }
            .star {
                height: 100%;
                background: url(data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAABgAAAAXCAMAAAAm/38fAAAAkFBMVEUAAADwwAfwwAfwwAfwwAfwwAfwwAfwwAfwwAfwwAfwwAfwwAfwwAfwwAfwwAfwwAfwwAfwwAfwwAfwwAfwwAfwwAfwwAfwwAfwwAfwwAfwwAfwwAfwwAfwwAfwwAfwwAfwwAfwwAfwwAfwwAfwwAfzzj3zzDT22GTxxRnxwxH22Wr11Vf00kzzzTnyyCn11VyMTJNWAAAAJHRSTlMAEPDrgHRa8+fiUjT69t7Vwr25kIiEfHluZ2AZFQXUmUI8KQgI9KuPAAAAw0lEQVQoz23RZxNDQBCA4T1ETdOihNTVS/7/v8tx4zi8X27XgzED5lRdN2Gn+wlRS3bARZoHm5LTABLZgIdYUlFgFdGw6taPEEv1r4h9hjRbUa0RLUdGVjsAS3Y+cJ6W/Jfj3AO08SzzpiiqBUgQDDe3TVHXHS46APj0qPosK3GOfbWCmwIYuyGPv4fL7nV4ifDmEIoQcXiK4HK4imBzkETQ+U9l+8U0L2wiwCJHuhhxSsfYoONxAohk4/BlYxqeZXUY/g/VKwgsd87AAAAAAElFTkSuQmCC) no-repeat 0 0;
                background-size: 24px 23px;
            }
        }
    }
</style>
