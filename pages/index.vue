<template>
    <v-layout>
        <v-flex>
            <v-card class="mx-auto" max-width="400" color="cyan lighten-5">
                <v-card-title class="headline justify-center">
                    <h1>
                        <span class="text-secondary-color"
                            ><strong>m</strong></span
                        ><span class="cyan--text">organa</span>
                        <!-- <strong>Meteo e previsioni</strong> -->
                    </h1>
                    <br />
                </v-card-title>
                <v-card-subtitle class="pt-3 pb-0 text-h6 text-center">
                    <strong>Meteo e previsioni</strong>
                </v-card-subtitle>
                <v-card-text class="pb-0">
                    <div class="text-center">
                        <weather-logo-animation />
                    </div>
                    <p class="text-subtitle-1">
                        Ottieni informazioni meteorologiche e previsioni su più
                        di 200.000 località nel mondo.
                    </p>
                </v-card-text>
                <hr class="my-3" />
                <v-card-actions>
                    <v-spacer />
                    <v-btn
                        ref="startButton"
                        dark
                        color="red accent-4"
                        nuxt
                        to="/search"
                    >
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
    mounted() {
        this.focusOnStartButton(); // setto il focus sul bottone "VAI!"
    },
    methods: {
        focusOnStartButton() {
            this.$refs.startButton.$el.focus();
        },
    },
};
</script>

<style lang="scss" scoped>
.prova {
    color: yellowgreen;
}
</style>
