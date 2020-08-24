<template>
    <div>
        <!-- menu a scomparsa laterale -->
        <v-navigation-drawer
            v-model="drawer"
            :clipped="clipped"
            fixed
            right
            temporary
            app
        >
            <v-list>
                <v-list-item
                    v-for="(item, i) in items"
                    :key="i"
                    :to="item.to"
                    router
                    exact
                >
                    <v-list-item-action>
                        <v-icon>{{ item.icon }}</v-icon>
                    </v-list-item-action>
                    <v-list-item-content>
                        <v-list-item-title v-text="item.title" />
                    </v-list-item-content>
                </v-list-item>
            </v-list>
        </v-navigation-drawer>

        <!-- navigation bar - header -->
        <v-app-bar
            :clipped-left="clipped"
            fixed
            dense
            dark
            color="cyan lighten-3"
            app
        >
            <nuxt-link class="" to="/">
                <v-img
                    class="mx-2"
                    :src="require('../assets/images/morgana_logo.png')"
                    max-height="35"
                    max-width="45"
                    contain
                ></v-img>
            </nuxt-link>

            <nuxt-link class="text-decoration-none" to="/">
                <v-card-title class="headline">
                    <span class="text-secondary-color"><strong>m</strong></span
                    ><span class="white--text">organa</span>
                </v-card-title>
            </nuxt-link>
            <v-spacer />
            <v-app-bar-nav-icon @click.stop="drawer = !drawer" />
        </v-app-bar>
    </div>
</template>

<script>
export default {
    data() {
        return {
            drawer: false,
            clipped: false,
            items: [
                {
                    icon: 'mdi-home',
                    title: 'Home',
                    to: '/',
                },
                {
                    icon: 'mdi-magnify',
                    title: 'Ricerca',
                    to: '/search',
                },
                {
                    icon: 'mdi-information',
                    title: 'Informazioni',
                    to: '/about',
                },
            ],
        };
    },
};
</script>

<style lang="scss" scoped>
//
</style>
