<template>
    <v-layout column justify-center align-center>
        <v-flex>
            <v-card color="cyan lighten-5">
                <!-- <nuxt-logo-animation /> -->
                <v-card-title class="headline text-center">
                    <span
                        ><v-icon
                            class="info-icon"
                            style="font-size: 30px; color: red;"
                            >mdi-information</v-icon
                        ></span
                    >&nbsp; Informazioni
                </v-card-title>
                <v-card-text>
                    <h5>SCOPO</h5>
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
                    <h5>FUNZIONAMENTO</h5>
                    <p>
                        Permette di ricercare informazioni meteo e previsioni su
                        più di 200.000 località nel mondo.<br />
                        Sfrutta delle chiamate API messe a disposizione da
                        <a
                            href="https://openweathermap.org/"
                            target="_blank"
                            rel="noopener noreferrer"
                            >OpenWeatherMap</a
                        >.
                    </p>
                    <h5>UTILIZZO</h5>
                    <p>
                        E' necessario inserire il nome di una località da
                        ricercare.<br />
                        E' possibile opzionalmente specificare anche un codice
                        della nazione, alcuni esempi:
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
                        qual caso è nesessario specificare il codice della
                        nazione<br />
                        (se non viene specificato il default è US), alcuni
                        esempi:
                    </p>
                    <ul>
                        <li>20148,it (corrisponde a Milano)</li>
                        <li>10156,it (corrisponde a Torino)</li>
                        <li>10156 (corrisponde a New York)</li>
                    </ul>
                    <br />
                    <h5>TECNOLOGIE</h5>
                    <ul>
                        <li>NUXT.JS</li>
                        <li>VUE.JS</li>
                        <li>VUETIFY</li>
                        <li>AXIOS</li>
                    </ul>
                    <br />
                    <h5>CREDITS</h5>
                    <p>
                        Dati meteo e previsioni forniti da
                        <br />
                        <a
                            href="https://openweathermap.org/"
                            target="_blank"
                            rel="noopener noreferrer"
                            ><img
                                class="logo-badge"
                                src="@/assets/images/owm_logo.png"
                                alt="owm logo"
                            />
                        </a>
                    </p>
                    <div>
                        Icone bandierine paesi create da
                        <a
                            href="http://www.freepik.com"
                            title="Freepik"
                            target="_blank"
                            rel="noopener noreferrer"
                            >Freepik</a
                        >
                        su
                        <a
                            href="https://www.flaticon.com/"
                            title="Flaticon"
                            target="_blank"
                            rel="noopener noreferrer"
                            >www.flaticon.com</a
                        >
                    </div>
                </v-card-text>
                <hr class="my-3" />
                <v-card-actions>
                    <v-spacer />
                    <v-btn dark color="red accent-4" nuxt to="/">
                        Home
                    </v-btn>
                    <v-btn dark color="red accent-4" nuxt to="/search">
                        Ricerca
                    </v-btn>
                </v-card-actions>
            </v-card>
        </v-flex>
    </v-layout>
</template>

<script>
export default {
    components: {},
};
</script>

<style lang="scss" scoped>
//
.logo-badge {
    width: 75px;
    height: 34px;
    vertical-align: text-bottom;
}
h5 {
    color: $secondary-color;
}
</style>
