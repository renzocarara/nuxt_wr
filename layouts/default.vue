<template>
    <v-app>
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

            <nuxt-link class="text-decoration-none text-secondary-color" to="/">
                <!-- <strong><v-toolbar-title v-text="title" /></strong> -->
                <v-card-title class="headline">
                    <span class="text-secondary-color"><strong>m</strong></span
                    ><span class="white--text">organa</span>
                </v-card-title>
            </nuxt-link>
            <v-spacer />

            <v-app-bar-nav-icon @click.stop="drawer = !drawer" />
        </v-app-bar>
        <!-- quella che segue è il corpo della pagina, cioè quello che sta fra l'header e il footer -->
        <v-main class="d-flex align-center bgc-main">
            <v-container>
                <nuxt />
            </v-container>
        </v-main>
        <!--  -->
        <v-footer :absolute="!fixed" dark color="cyan lighten-3" app>
            <span>
                <a
                    href="https://www.renzocarara.it/"
                    target="_blank"
                    rel="noopener noreferrer"
                >
                </a
                >Renzo Carara - &copy; {{ new Date().getFullYear() }}</span
            >
        </v-footer>
    </v-app>
</template>

<script>
export default {
    data() {
        return {
            clipped: false,
            drawer: false,
            fixed: false,
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
            title: 'morgana',
        };
    },
};
</script>
<style lang="scss" scoped>
//
.bgc-main {
    background-color: $cyan-lighten-4;
}
</style>
