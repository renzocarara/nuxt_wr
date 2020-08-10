<template>
    <v-layout>
        <v-flex>
            <v-card class="mx-auto" max-width="400">
                <div class="text-center">
                    <weather-logo-animation />
                </div>
                <v-card-title class="headline">
                    <span class="text-secondary-color">N</span>uxt&nbsp;
                    <span class="text-secondary-color">W</span>eather&nbsp;
                    <span class="text-secondary-color">R</span>eport&nbsp;
                </v-card-title>
                <v-card-text>
                    <h3>Meteo e previsioni</h3>
                    <hr class="my-3" />
                    <p>
                        Ottieni informazioni meteorologiche e previsioni su più
                        di 200000 località nel mondo.
                    </p>
                </v-card-text>
                <v-card-actions>
                    <v-spacer />
                    <v-btn color="primary" nuxt to="/search">
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
