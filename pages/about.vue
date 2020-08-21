<template>
    <v-layout column justify-center align-center>
        <v-flex>
            <v-card color="cyan lighten-5" max-width="800">
                <v-card-title class="headline justify-center">
                    <h3>
                        <v-icon size="40" color="red accent-4"
                            >mdi-information</v-icon
                        >

                        <span class="text-secondary-color"
                            ><strong>m</strong></span
                        ><span class="cyan--text">organa</span>
                        <span class="text-subtitle-1"> v1.0.0</span>
                    </h3>
                </v-card-title>
                <v-card-text>
                    <h4>SCOPO</h4>
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
                    <h4>FUNZIONAMENTO</h4>
                    <p>
                        Permette di ricercare informazioni meteo e previsioni su
                        più di 200.000 località nel mondo.<br />
                        Sfrutta delle API messe a disposizione da
                        <a
                            href="https://openweathermap.org/"
                            target="_blank"
                            rel="noopener noreferrer"
                            >OpenWeatherMap</a
                        >.
                    </p>
                    <h4>UTILIZZO</h4>
                    <p>
                        E' necessario inserire il nome di una località da
                        ricercare.<br />
                        E' possibile opzionalmente specificare anche un codice
                        della nazione (ISO 3166 country code), alcuni esempi:
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
                    <p>
                        E' possibile definire una località come "preferita",
                        flaggando la stellina (checkbox) accanto al nome della
                        località. Al boot dell'applicazione questa verrà
                        mostrata all'utente in automatico.
                    </p>
                    <br />
                    <h4>TECNOLOGIE</h4>
                    <ul>
                        <li>NUXT.JS v2.14.1</li>
                        <li>VUE.JS v2.6.11</li>
                        <li>VUETIFY v2.3.7</li>
                        <li>AXIOS v0.19.2</li>
                    </ul>
                    <br />
                    <h4>CREDITS</h4>
                    <p>
                        Dati meteo
                        <br />
                        <a
                            href="https://openweathermap.org/"
                            target="_blank"
                            rel="noopener noreferrer"
                            ><img
                                class="owm-logo"
                                src="@/assets/images/owm_logo.png"
                                alt="owm logo"
                            />
                        </a>
                    </p>
                    <p>
                        Country flags by <br />
                        <a
                            href="http://www.freepik.com"
                            title="Freepik"
                            target="_blank"
                            rel="noopener noreferrer"
                            ><img
                                class="vert-align-sub"
                                src="@/assets/images/freepik_logo.png"
                                alt="freepik logo"
                        /></a>
                        &nbsp;from&nbsp;
                        <a
                            href="https://www.flaticon.com/"
                            title="Flaticon"
                            target="_blank"
                            rel="noopener noreferrer"
                            ><img
                                class="vert-align-sub"
                                src="@/assets/images/flaticon_logo.png"
                                alt="flaticon logo"
                        /></a>
                    </p>
                    <p>
                        Deployed on
                        <br />
                        <a
                            href="https://netlify.com/"
                            target="_blank"
                            rel="noopener noreferrer"
                            ><img
                                class="netlify-logo"
                                src="@/assets/images/netlify_logo.png"
                                alt="netlify logo"
                            />
                        </a>
                    </p>
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
.owm-logo {
    width: 75px;
    height: 34px;
    vertical-align: text-bottom;
}
.netlify-logo {
    width: 98px;
    height: 35px;
    vertical-align: text-bottom;
}
.vert-align-sub {
    vertical-align: sub;
}
h4 {
    color: $secondary-color;
}
p,
ul {
    font-size: 16px;
}
</style>
