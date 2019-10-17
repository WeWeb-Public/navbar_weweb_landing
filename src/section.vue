<!-- This is a Vue.js single file component. -->
<!-- Check the Vue.js doc here :  -->
<!-- https://vuejs.org/v2/guide/ -->

<!-- This is your HTML -->
<template>
    <div class="navbar_A" ww-fixed>
        <div class="navbar-top">
            <div class="container">
                <!-- wwManager:start -->
                <wwSectionEditMenu size="small" :sectionCtrl="sectionCtrl" :options="openOptions"></wwSectionEditMenu>
                <!-- wwManager:end -->
                <!-- Weweb Wallpaper -->
                <div class="content" :class="{'shadow':scrollStarted}">
                    <wwObject v-if="scrollStarted" class="background" :ww-object="section.data.background" ww-category="background"></wwObject>

                    <!-- LOGO -->
                    <div class="logo-container">
                        <div class="logo" :class="{'smaller':scrollStarted}">
                            <wwObject :ww-object="section.data.logo" ww-no-twic-pics="true"></wwObject>
                        </div>
                    </div>

                    <!-- LINKS -->
                    <wwLayoutColumn v-if="!scrollStarted" class="left-row" tag="div" ww-default="ww-row" :ww-list="section.data.leftRowAtScrollTop" @ww-add="add(section.data.leftRowAtScrollTop, $event)" @ww-remove="remove(section.data.leftRowAtScrollTop, $event)">
                        <wwObject v-for="row in section.data.leftRowAtScrollTop" :key="row.uniqueId" :ww-object="row"></wwObject>
                    </wwLayoutColumn>

                    <!-- CALL TO ACTION -->
                    <wwLayoutColumn v-if="!scrollStarted" class="right-row" tag="div" ww-default="ww-row" :ww-list="section.data.rightRowAtScrollTop" @ww-add="add(section.data.rightRowAtScrollTop, $event)" @ww-remove="remove(section.data.rightRowAtScrollTop, $event)">
                        <wwObject v-for="row in section.data.rightRowAtScrollTop" :key="row.uniqueId" :ww-object="row"></wwObject>
                    </wwLayoutColumn>

                    <!-- LINKS AFTER SCROLL -->
                    <transition name="slide-fade">
                        <wwLayoutColumn v-if="scrollStarted" class="left-row" tag="div" ww-default="ww-row" :ww-list="section.data.leftRow" @ww-add="add(section.data.leftRow, $event)" @ww-remove="remove(section.data.leftRow, $event)">
                            <wwObject v-for="row in section.data.leftRow" :key="row.uniqueId" :ww-object="row"></wwObject>
                        </wwLayoutColumn>
                    </transition>
                    <!-- CALL TO ACTION AFTER SCROLL -->
                    <wwLayoutColumn v-if="scrollStarted" class="right-row" tag="div" ww-default="ww-row" :ww-list="section.data.rightRow" @ww-add="add(section.data.rightRow, $event)" @ww-remove="remove(section.data.rightRow, $event)">
                        <wwObject v-for="row in section.data.rightRow" :key="row.uniqueId" :ww-object="row"></wwObject>
                    </wwLayoutColumn>

                    <!-- MOBILE MENU -->
                    <div class="menu-container">
                        <wwObject class="menu-icon" :ww-object="section.data.mobileMenu"></wwObject>
                    </div>
                </div>
            </div>
        </div>
        <div class="navbar-side">
            <div class="container">
                <!-- wwManager:start -->
                <wwSectionEditMenu size="small" :sectionCtrl="sectionCtrl" :options="openOptions"></wwSectionEditMenu>
                <!-- wwManager:end -->
                <wwObject class="background" :ww-object="section.data.backgroundSide" ww-category="background"></wwObject>

                <wwLayoutColumn tag="div" ww-default="ww-row" :ww-list="section.data.rowsSide" class="content" @ww-add="add(section.data.rowsSide, $event)" @ww-remove="remove(section.data.rowsSide, $event)">
                    <wwObject v-for="row in section.data.rowsSide" :key="row.uniqueId" :ww-object="row"></wwObject>
                </wwLayoutColumn>
            </div>
        </div>
        <div class="navbar-cover" :class="{'show': navbarOpen}" @click="toggleNavbar"></div>
    </div>
</template>

<!-- This is your Javascript -->
<!-- ✨ Here comes the magic ✨ -->
<script>

export default {
    name: "__COMPONENT_NAME__",
    props: {
        // The section controller object is passed to you.
        sectionCtrl: Object
    },
    data() {
        return {
            windowHeight: 0,
            show: false,
            navbarOpen: false,
            scrollStarted: false
        }
    },
    computed: {
        //Get the section object here !
        // It contains all the info and data about the section
        // Use it has you like !
        section() {
            return this.sectionCtrl.get();
        }
    },
    methods: {
        init() {
            if (window.location.pathname == '/welcome' || window.location.pathname == '/ww_front/welcome') {
                window.addEventListener('scroll', this.onScroll, { passive: true })
            } else {
                this.scrollStarted = true;
            }
            window.addEventListener('resize', this.onResize, { passive: true });

            wwLib.$on('wwNavbar:toggle', this.toggleNavbar);
        },

        /*=============================================m_ÔÔ_m=============================================\
          TOGGLE NAVBAR SIDE
        \================================================================================================*/
        toggleNavbar() {
            this.navbarOpen = !this.navbarOpen;
            if (this.navbarOpen) {
                for (let section of document.querySelectorAll('.ww-section:not([ww-fixed])')) {
                    section.classList.add('navbar_A-open');
                }
                for (let container of this.$el.querySelectorAll('.container')) {
                    container.classList.add('navbar_A-open');
                }
            }
            else {
                for (let section of document.querySelectorAll('.ww-section:not([ww-fixed])')) {
                    section.classList.remove('navbar_A-open');
                }
                for (let container of this.$el.querySelectorAll('.container')) {
                    container.classList.remove('navbar_A-open');
                }
            }
        },

        /*=============================================m_ÔÔ_m=============================================\
          SHOW / HIDE NAVBAR TOP
        \================================================================================================*/
        onScroll() {
            try {
                const scrollTop = Math.max(document.documentElement.scrollTop, document.body.scrollTop);
                this.scrollStarted = (scrollTop > 0);
            } catch (error) {
                console.log(error)
            }
        },
        onResize() {
            const e = document.documentElement;
            const g = document.getElementsByTagName('body')[0];
            this.windowHeight = window.innerHeight || e.clientHeight || g.clientHeight;
        },

        /*=============================================m_ÔÔ_m=============================================\
          ADD / REMOVE ROWS
        \================================================================================================*/
        /* wwManager:start */
        add(list, options) {
            list.splice(options.index, 0, options.wwObject);

            this.sectionCtrl.update(this.section);
        },
        remove(list, options) {
            list.splice(options.index, 1);

            this.sectionCtrl.update(this.section);
        },
    },
    mounted() {
        this.init();
    },
    created() {

        let needUpdate = false;

        //Initialize section data
        this.section.data = this.section.data || {};


        if (!this.section.data.background) {
            this.section.data.background = wwLib.wwObject.getDefault({ type: 'ww-color', data: { backgroundColor: '#FFFFFF' } });
            needUpdate = true;
        }

        if (!this.section.data.logo) {
            this.section.data.logo = wwLib.wwObject.getDefault({ type: 'ww-image' });
            needUpdate = true;
        }

        if (!this.section.data.mobileMenu) {
            this.section.data.mobileMenu = wwLib.wwObject.getDefault({ type: 'ww-icon' });
            needUpdate = true;
        }

        if (!this.section.data.leftRow) {
            this.section.data.leftRow = [];
            needUpdate = true;
        }

        if (!this.section.data.rightRow) {
            this.section.data.rightRow = [];
            needUpdate = true;
        }

        if (!this.section.data.leftRowAtScrollTop) {
            this.section.data.leftRowAtScrollTop = [];
            needUpdate = true;
        }

        if (!this.section.data.rightRowAtScrollTop) {
            this.section.data.rightRowAtScrollTop = [];
            needUpdate = true;
        }

        if (!this.section.data.rowsSide) {
            this.section.data.rowsSide = [];
            needUpdate = true;
        }

        if (!this.section.data.backgroundSide) {
            this.section.data.backgroundSide = wwLib.wwObject.getDefault({ type: 'ww-color', data: { backgroundColor: '#FFFFFF' } });
            needUpdate = true;
        }


        if (needUpdate) {
            this.sectionCtrl.update(this.section);
        }


        this.onResize();

    },
    beforeDestroy() {
        window.removeEventListener('scroll', this.onScroll);
        window.removeEventListener('resize', this.onResize);

        wwLib.$off('wwNavbar:toggle', this.toggleNavbar);
    }
};
</script>

<style lang="scss">
.navbar_A-open {
    transform: translateX(-200px);
}

.ww-section:not([ww-fixed]) {
    transition: transform 0.3s ease;
}
</style>

<style scoped lang="scss">
$navbar-width: 330px;

.navbar_A {
    width: 100%;

    .navbar-cover {
        display: none;
        pointer-events: all;
        width: 100%;
        position: fixed;
        top: 0;
        bottom: 0;
        z-index: 100;

        &.show {
            display: block;
        }
    }

    .navbar-top {
        width: 100%;
        position: fixed;
        top: 0;
        z-index: 101;

        .container {
            width: 100%;
            height: 70px;
            transition: transform 0.3s ease;

            &.navbar_A-open {
                transform: translateX(-$navbar-width);
            }

            .background {
                position: absolute;
                top: 0;
                left: 0;
                height: 100%;
                width: 100%;
            }
            .content {
                position: relative;
                display: flex;
                align-items: center;
                transition: box-shadow 0.3s ease;
                &.shadow {
                    box-shadow: 0 3px 10px 3px #dedede;
                }
                .logo-container {
                    padding-left: 5%;
                    transition: all 2s ease;
                    flex-basis: 20%;
                    .logo {
                        width: 110px;
                        &.smaller {
                            transform: scale3d(0.8, 0.8, 0.8);
                        }
                    }
                }

                .left-row {
                    display: none;
                    width: auto;
                    flex-basis: 60%;
                    @media (min-width: 992px) {
                        display: block;
                    }
                }
                .right-row {
                    display: none;
                    flex-basis: 20%;
                    width: 500px;
                    @media (min-width: 992px) {
                        display: block;
                    }
                }
                .menu-container {
                    display: flex;
                    flex-basis: 80%;
                    justify-content: flex-end;
                    padding-right: 20px;
                    @media (min-width: 992px) {
                        display: none;
                    }
                }
            }
        }
    }

    .navbar-side {
        position: fixed;
        top: 0;
        left: 100%;
        width: $navbar-width;
        z-index: 101;
        height: 100%;

        .container {
            width: 100%;
            height: 100%;
            transition: transform 0.3s ease;

            &.navbar_A-open {
                transform: translateX(-$navbar-width);
            }

            .background {
                position: absolute;
                top: 0;
                left: 0;
                height: 100%;
                width: 100%;
            }

            .content {
                position: relative;
            }

            .edit-menu-container {
                position: absolute;
                top: 0;
                left: 0;
                width: 100%;
                height: 60px;
                overflow-x: hidden;
            }
        }
    }
    .slide-fade-enter-active {
        transition: all 0.3s ease;
    }
    .slide-fade-enter {
        transform: translateX(10px);
        opacity: 0;
    }
}
</style>
