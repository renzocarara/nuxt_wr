<template>
    <v-layout>
        <v-flex>
            <v-card class="mx-auto" max-width="400" color="cyan lighten-5">
                <v-card-title class="headline">
                    <span class="text-secondary-color"><strong>N</strong></span
                    >uxt&nbsp;
                    <span class="text-secondary-color"><strong>W</strong></span
                    >eather&nbsp;
                    <span class="text-secondary-color"><strong>R</strong></span
                    >eport&nbsp;
                </v-card-title>
                <v-card-subtitle
                    ><strong>Meteo e previsioni</strong></v-card-subtitle
                >
                <v-card-text>
                    <div class="text-center">
                        <weather-logo-animation />
                    </div>
                    <p>
                        Ottieni informazioni meteorologiche e previsioni su più
                        di 200.000 località nel mondo.
                    </p>
                </v-card-text>
                <hr class="my-3" />
                <v-card-actions>
                    <v-spacer />
                    <v-btn dark color="red accent-4" nuxt to="/search">
                        Vai!
                    </v-btn>
                </v-card-actions>
            </v-card>
        </v-flex>
    </v-layout>
</template>

<script>
import WeatherLogoAnimation from '~/components/WeatherLogoAnimation.vue';

export default {
    components: {
        WeatherLogoAnimation,
    },
};
</script>

<style lang="scss" scoped>
.prova {
    color: yellowgreen;
}
</style>
