<template>
    <div id="app" class="wrapper" v-bind:class="{ 'navigation-closed': !navActive }">
        <ul class="back-lines container">
            <li class="line"></li>
            <li class="line"></li>
            <li class="line"></li>
            <li class="line"></li>
        </ul>
        <site-header 
            :lang="lang">      
        </site-header>
        <site-navigaion
            :lang="lang"
            :navActive="navActive"
            :navItems="navItems"/>
        </site-navigaion>
        <router-view class="view" 
            :langData="langData"
            :navActive="navActive"
            :navItems="navItems"
            :lang="lang">
        </router-view>
    </div>
</template>

<script>
    import siteNavigaion from './components/navigation/navigation.vue';
    import siteHeader from './components/header/header.vue';

    import {langFile} from './config/lang.js';
    import {pages} from './config/pages.js';

    window.pages = pages;   

    export default {
        name: 'app',
        components: {
            siteNavigaion,
            siteHeader
        },
        data(){
            return {
                navActive: true,
                lang: 'ka',
                navItems: []
            }
        },
        watch:{
            $route (to, from) {
                this.watchURL();
            }
        },
        created(){
            bus.$on('navActive', (state) => {
                this.navActive = state;
            });
            
            let lang = localStorage.getItem("lang");

            if(lang){
                this.lang = lang;
            }
            // this.fetchNavItems()
;            this.watchURL();
            this.fetchNavItems();
        },
        computed: {
            langData(){
                return langFile[this.lang];
            },
        },
        methods: {
            checkLang(lang){
                if(lang!='en' && lang!='ka'){
                    this.$router.push('/');
                }
                else{
                    this.lang = lang;
                    localStorage.setItem("lang",lang);
                }
            },
            watchURL(){
                if(this.$route.path=="/"){
                    this.$router.push('/'+this.lang);
                }
                this.checkLang(this.$route.params.lang);
            },
            fetchNavItems(){
                let self = this;
                this.$http.get('/api/product').then((res) => {
                    self.navItems = res.data;
                });
            }
        }
    };
</script>

<style lang="sass">
    @import "../sass/helpers/mixins";
    @import "../sass/helpers/variables";

    .flashLight{
        background: #fff;
        width: 100%;
        height: 100%;
        position: absolute;
        opacity: 0;
        z-index: 0;
    }

    .hide{
        display: none;
    }

    .back-lines{
        list-style: none;
        position: fixed;
        height: 100%;
        z-index: 0;
        padding: 0;
        @include center-horizontally;

        @media (max-width: $breakpoint-phone) {
            width: 98%;
        }

        .line{
            display:inline-block;
            transform-origin: top;
            transform: scale(1,0);
            width: 2px;
            background: #efefef;
            height: 100%;
            position: absolute;
            animation: drawBackLine ease-out forwards;
            animation-delay: 250ms;
        }

        @for $i from 1 through 4{
            .line:nth-child(#{$i}){
                left: ($i - 1)*33.3 + %;
                animation-duration: 1000 + random(1000) + ms;
            }
        }
    }

    .wrapper{
        height: 100%;
    }
    .wrapper.navigation-closed{
        height: initial;
        
        .navigation{
            height: 0;
            z-index: 0;

            .bound,
            .lens{
                display: none;
            }

            .camera{
                animation: goTop 1s 500ms forwards;
                z-index: 0;

                a:first-child{
                    cursor: pointer;
                }

                svg{
                    @include transform(scale(0.3));
                    transition: transform 1000ms 500ms;

                    @media (max-width: $breakpoint-phone) {
                        @include transform(scale(0.5));
                    }
                }
                
                .gallery-link{
                    display: none;
                }
            }
        }
    }
</style>