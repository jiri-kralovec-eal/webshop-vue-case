<template>  

    <header :class="homepage()">
        <div class="shop-content" v-if="fetched">
            <template v-if="this.$route.name == 'homepage'">
                
                <div class="header__interactive">
                    <router-link tag="button" class="btn btn--default" :to="siteHeader.linkurl" :title="siteHeader.link">{{ siteHeader.link }}</router-link>
                </div>
                <div class="header__splash-art"></div>
                <div class="header__background-art" :style="'background-image: url(' + siteHeader.splash + ')'"></div>

            </template>
            <template v-else>

                <h1 class="header__title" v-if="isSearch">{{ this.$route.params.query }}</h1>
                <h1 class="header__title" v-else-if="viewbag && viewbag.hasOwnProperty('title')">{{ viewbag.title }}</h1>

                <h2 class="header__subtitle" v-if="viewbag && viewbag.subtitle">{{ viewbag.subtitle }}</h2>

                <ul class="header__breadcrumbs" v-if="viewbag && (viewbag.subtitle == null || viewbag.subtitle == '') && viewbag.breadcrumbs && viewbag.breadcrumbs.length > 1">
                    <li class="breadcrumbs__crumb" v-for="(crumb, index) in viewbag.breadcrumbs">
                        <template v-if="index === viewbag.breadcrumbs.length - 1">
                            <router-link tag="a" class="breadcrumb__link" :to="crumb.path">{{ crumb.title }}</router-link>
                        </template>
                        <template v-else>
                            <span class="breadcrumb__link">{{ crumb.title }}</span>
                        </template>
                    </li>
                </ul>
            
            </template>
        </div>
    </header>

</template>

<script>

    import { EventBus } from "@/event-bus";

    export default {
        data() {
            return {
                fetched: false,
                viewbag: null
            }
        },  
        methods: {
            homepage() {
                let prefix = 'site-header';
                let page = this.$route.name == 'homepage' ? 'header--homepage' : 'header--default';

                return prefix + ' ' + page;
            }
        },
        mounted() {
            EventBus.$emit('header-request');
        },
        created() {

            EventBus.$on('header-resolve', (data) => {
                this.fetched = true;
                this.viewbag = data;
            });

        },
        computed: {
            isSearch() {
                return this.$route.name == 'search' && this.$route.params.query;
            },
            siteHeader() {
                return {
                    splash: this.$root.shop.promotions.homepage.headerbg,
                    link: this.$root.shop.promotions.homepage.promotionLinkTitle,
                    linkurl: this.$root.shop.promotions.homepage.promotionLinkUrl,
                }
            }
        }
    }

</script>   