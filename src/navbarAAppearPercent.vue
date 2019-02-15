<template>
    <div class="navbar-a-appear-percent">
        <div class="title">{{wwLang.getText('scrollTitle')}}</div>
        <div class="desc">{{wwLang.getText('scrollDesc')}}</div>
        <div class="desc">{{wwLang.getText('scrollDesc2')}}</div>
        <wwManagerInput class="input" color="green" v-model="scrollPercent" :label="wwLang.getText('percent')" @change="setScrollPercent($event)"></wwManagerInput>
    </div>
</template>

<script> 
import langPopupNavbarA from './langPopupNavbarA.json';

export default {
    name: "navbarAApearPercent",
    props: {
        options: {
            type: Object,
            default: function () {
                return {}
            }
        },
    },
    data() {
        return {
            wwLang: wwLib.wwManagerLang.use(langPopupNavbarA),
            scrollPercent: -1
        };
    },
    computed: {
    },
    watch: {
    },
    methods: {
        init() {
            this.scrollPercent = this.options.data.navbarAPercent || -1;
            this.options.result.navbarAPercent = this.options.data.navbarAPercent || -1;
        },
        setScrollPercent(value) {
            this.scrollPercent = value;
            this.options.result.navbarAPercent = value == -1 ? null : value;
        }
    },
    mounted() {
        this.init()
    },
    beforeDestroy() {

    }
};
</script>

<style scoped lang="scss">
.navbar-a-appear-percent {
    padding: 30px;
    font-family: "Monserrat", sans-serif;

    .title {
        margin-bottom: 20px;
    }
    .desc {
        color: #a7a7a7;
        margin-bottom: 10px;
    }
    .input {
        margin-top: 20px;
    }
}
</style>
