<template>
    <v-layout column justify-center align-center>
        <v-flex>
            <v-card>
                <nuxt-logo-animation />
                <v-card-title class="headline">
                    <span class="text-secondary-color">N</span>uxt&nbsp;
                    <span class="text-secondary-color">W</span>eather&nbsp;
                    <span class="text-secondary-color">R</span>eport&nbsp;
                </v-card-title>
                <v-card-text>
                    <h3>
                        <span><v-icon>mdi-information</v-icon></span>
                        Informazioni
                    </h3>
                    <p>
                        Questa applicazione è un esercizio sull'utilizzo del
                        framework
                        <a
                            href="https://nuxtjs.org/"
                            target="_blank"
                            rel="noopener noreferrer"
                            >Nuxt.js
                        </a>
                    </p>
                    <p>
                        Permette di ricercare informazioni meteo e previsioni su
                        più di 200000 località nel mondo. Sfrutta delle chiamate
                        API messe a disposizione da
                        <a
                            href="https://openweathermap.org/"
                            target="_blank"
                            rel="noopener noreferrer"
                            >OpenWeatherMap</a
                        >.
                    </p>
                    <p>
                        UTILIZZO: è necessario inserire il nome di una località
                        da ricercare. E' possibile opzionalmente specificare
                        anche un codice del paese , alcuni esempi:
                    </p>
                    <ul>
                        <li>roma,it</li>
                        <li>milano</li>
                        <li>san jose,ar</li>
                        <li>san jose,us</li>
                    </ul>
                    <br />
                    <p>
                        E' possibile anche ricercare tramite codice postale, nel
                        qual caso è nesessario specificare il codice di un paese
                        (se non viene specificato il default è US) esempi:
                    </p>
                    <ul>
                        <li>20148,it (corrisponde a Milano)</li>
                        <li>10156,it (corrisponde a Torino)</li>
                        <li>10156 (corrisponde a New York)</li>
                    </ul>

                    <hr class="my-3" />
                    <p>
                        Dati meteo e previsioni forniti da
                        <a
                            href="https://openweathermap.org/"
                            target="_blank"
                            rel="noopener noreferrer"
                            ><img
                                class="logo-img"
                                src="@/assets/images/owm_logo.png"
                                alt="owm logo"
                            />
                        </a>
                    </p>
                </v-card-text>
                <v-card-actions>
                    <v-spacer />
                    <v-btn color="primary" nuxt to="/">
                        Back Home
                    </v-btn>
                </v-card-actions>
            </v-card>
        </v-flex>
    </v-layout>
</template>

<script>
import NuxtLogoAnimation from '~/components/NuxtLogoAnimation.vue';

export default {
    components: {
        NuxtLogoAnimation,
    },
};
</script>

<style lang="scss" scoped>
//
.logo-img {
    width: 75px;
    height: 34px;
    vertical-align: text-bottom;
}
</style>
