<template>
    <v-app>
        <!-- header bar -->
        <the-header />

        <!-- quella che segue è il corpo della pagina, cioè quello che sta fra l'header e il footer -->
        <v-main class="d-flex align-center bgc-main">
            <v-container>
                <nuxt />
            </v-container>
        </v-main>

        <!-- footer -->
        <the-footer />
    </v-app>
</template>

<script>
import TheFooter from '~/components/TheFooter.vue';
import TheHeader from '~/components/TheHeader.vue';

export default {
    components: {
        TheFooter,
        TheHeader,
    },
};
</script>
<style lang="scss" scoped>
//
.bgc-main {
    background-color: $cyan-lighten-4;
}
</style>
