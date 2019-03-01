<!-- This is a Vue.js single file component. -->
<!-- Check the Vue.js doc here :  -->
<!-- https://vuejs.org/v2/guide/ -->

<!-- This is your HTML -->
<template>
    <div class="navbar_A" ww-fixed>
        <div class="placeholder" v-if="section.data.appearPercent == null">
            <!-- wwManager:start -->
            <div class="placeholder-infos">Placeholder for navbar_A
                <br>Place navbar_A on top of the section list to hide this
            </div>
            <!-- wwManager:end -->
        </div>
        <div class="navbar-top" :class="{'show': show, 'no-anim': section.data.appearPercent == null}">
            <div class="container">
                <!-- wwManager:start -->
                <wwSectionEditMenu size="small" :sectionCtrl="sectionCtrl" :options="openOptions"></wwSectionEditMenu>
                <!-- wwManager:end -->
                <!-- Weweb Wallpaper -->
                <wwObject class="background" :ww-object="section.data.background" ww-category="background"></wwObject>

                <wwLayoutColumn tag="div" ww-default="ww-row" :ww-list="section.data.rows" class="content" @ww-add="add(section.data.rows, $event)" @ww-remove="remove(section.data.rows, $event)">
                    <wwObject v-for="row in section.data.rows" :key="row.uniqueId" :ww-object="row"></wwObject>
                </wwLayoutColumn>
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
/* wwManager:start */
import navbarAAppearPercent from './navbarAAppearPercent.vue'
wwLib.wwPopups.addPopup('navbarAAppearPercent', navbarAAppearPercent);
wwLib.wwPopups.addStory('NAVBAR_A_APPEAR_PERCENT', {
    title: {
        en: 'Scroll appear percent',
        fr: 'Pourcentage de scroll avant apparition'
    },
    type: 'navbarAAppearPercent',
    buttons: {
        FINISH: {
            text: {
                en: 'Finish',
                fr: 'Terminer'
            },
            next: false
        }
    }
});
/* wwManager:end */

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
            navbarOpen: false
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
            window.addEventListener('scroll', this.onScroll);
            window.addEventListener('resize', this.onResize);

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
            this.setScrollPercent();
        },
        onResize() {
            const e = document.documentElement;
            const g = document.getElementsByTagName('body')[0];
            this.windowHeight = window.innerHeight || e.clientHeight || g.clientHeight;

            this.setScrollPercent();
        },
        setScrollPercent() {
            if (this.section.data.appearPercent == null) {
                this.show = true;
                return;
            }

            const scrollTop = Math.max(document.documentElement.scrollTop, document.body.scrollTop);

            let scrollPercent = Math.max(0, 100 * scrollTop / this.windowHeight) - 0.0001;

            if (this.windowHeight + scrollTop >= document.body.clientHeight) {
                scrollPercent = 99999999;
            }

            this.show = scrollPercent >= this.section.data.appearPercent;
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

        async openOptions() {
            let options = {
                firstPage: 'NAVBAR_A_APPEAR_PERCENT',
                data: {
                    navbarAPercent: this.section.data.appearPercent
                },
            }

            try {

                const result = await wwLib.wwPopups.open(options)

                if (typeof (result.navbarAPercent) !== 'undefinded') {
                    this.section.data.appearPercent = result.navbarAPercent;
                    this.sectionCtrl.update(this.section);
                    this.onScroll();
                }

            } catch (error) {

            }
        }
        /* wwManager:end */
    },
    mounted() {
        this.init();
    },
    created() {

        let needUpdate = false;

        //Initialize section data
        this.section.data = this.section.data || {};

        if (!this.section.data.appearPercent) {
            this.section.data.appearPercent = null;
            needUpdate = false;
        }

        if (!this.section.data.rows) {
            this.section.data.rows = [];
            needUpdate = true;
        }
        if (!this.section.data.rowsSide) {
            this.section.data.rowsSide = [];
            needUpdate = true;
        }

        if (!this.section.data.background) {
            this.section.data.background = wwLib.wwObject.getDefault({ type: 'ww-color', data: { backgroundColor: '#FFFFFF' } });
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
$navbar-width: 400px;

.navbar_A {
    width: 100%;

    .placeholder {
        min-height: 70px;
        position: relative;

        /* wwManager:start */
        .placeholder-infos {
            position: absolute;
            top: 0;
            left: 0;
            bottom: 0;
            right: 0;
            background-color: #d6d6d6;
            display: flex;
            justify-content: center;
            align-items: center;
            color: black;
            text-align: center;
        }
        /* wwManager:end */
    }

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

        transition: transform 0.3s ease;
        transform: translateY(calc(-100% - 10px));

        &.show {
            transform: translateY(0);
        }

        .container {
            width: 100%;
            height: 70px;
            transition: transform 0.3s ease;

            &.navbar_A-open {
                transform: translateX(-$navbar-width);
            }

            &.no-anim {
                transition: none;
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
}
</style>
