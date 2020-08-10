<template>
    <v-layout>
        <v-flex class="text-center">
            <v-card class="mx-auto" max-width="400">
                <v-text-field
                    v-model="place"
                    class="d-inline-block px-0 py-10"
                    placeholder="Inserisci una località..."
                    clearable
                    persistent-hint
                    :label="label"
                    :hint="hint"
                    :loading="loading"
                    append-icon="mdi-magnify"
                    @click:append="handleSearch"
                    @keypress.13="handleSearch"
                ></v-text-field>
                <div v-if="resultsAvailable">
                    <v-list-item three-line>
                        <v-list-item-content>
                            <v-list-item-title class="headline">
                                <strong>{{ city }}</strong
                                >, {{ country }}
                            </v-list-item-title>
                            <v-list-item-subtitle
                                >{{ dt }}
                            </v-list-item-subtitle>
                            <v-list-item-subtitle>
                                <strong class="text-h5">{{
                                    description
                                }}</strong>
                            </v-list-item-subtitle>
                        </v-list-item-content>
                    </v-list-item>

                    <v-card-text>
                        <v-row align="center">
                            <v-col class="text-right" cols="6">
                                <span class="text-subtitle-2 blue--text"
                                    >{{ tempMin }}&nbsp;
                                </span>
                                <span class="text-subtitle-2 red--text">
                                    {{ tempMax }}</span
                                ><br />
                                <span class="display-1">{{ temp }}&deg;C</span
                                ><br />
                                <span class="text-subtitle-2 grey--text"
                                    >percepita {{ feelsLike }}</span
                                >
                            </v-col>
                            <v-col cols="6" class="">
                                <v-img
                                    class=""
                                    src="https://cdn.vuetifyjs.com/images/cards/sun.png"
                                    alt="Sunny image"
                                    width="92"
                                ></v-img>
                            </v-col>
                        </v-row>
                    </v-card-text>

                    <v-list-item>
                        <v-list-item-content>
                            <v-list-item-subtitle
                                ><v-icon>mdi-weather-windy</v-icon>&nbsp;vento
                                {{ windSpeed }}m/s&nbsp;
                                <v-icon>mdi-compass-outline</v-icon
                                >&nbsp;direzione
                                {{ windDeg }}&deg;</v-list-item-subtitle
                            >

                            <v-list-item-subtitle
                                ><v-icon>mdi-water-percent</v-icon>&nbsp;umidità
                                {{ humidity }}%</v-list-item-subtitle
                            >

                            <v-list-item-subtitle
                                ><v-icon>mdi-format-line-weight</v-icon
                                >&nbsp;pressione
                                {{ pressure }}hPa</v-list-item-subtitle
                            >

                            <v-list-item-subtitle
                                ><v-icon>mdi-sunglasses</v-icon>&nbsp;indice UV
                                {{ uvi }}
                            </v-list-item-subtitle>

                            <v-list-item-subtitle
                                ><v-icon>mdi-weather-sunset-up</v-icon>&nbsp;il
                                sole sorge alle
                                {{ sunrise }}
                            </v-list-item-subtitle>

                            <v-list-item-subtitle
                                ><v-icon>mdi-weather-sunset-down</v-icon
                                >&nbsp;il sole tramonta alle
                                {{ sunset }}
                            </v-list-item-subtitle>
                        </v-list-item-content>
                    </v-list-item>

                    <!-- <v-slider
                        v-model="time"
                        :max="6"
                        :tick-labels="labels"
                        class="mx-4"
                        ticks
                    ></v-slider>

                    <v-list class="transparent">
                        <v-list-item v-for="item in forecast" :key="item.day">
                            <v-list-item-title>{{
                                item.day
                            }}</v-list-item-title>

                            <v-list-item-icon>
                                <v-icon>{{ item.icon }}</v-icon>
                            </v-list-item-icon>

                            <v-list-item-subtitle class="text-right">
                                {{ item.temp }}
                            </v-list-item-subtitle>
                        </v-list-item>
                    </v-list>

                    <v-divider></v-divider>

                    <v-card-actions>
                        <v-btn text>Full Report</v-btn>
                    </v-card-actions> -->
                </div>
            </v-card>
        </v-flex>
    </v-layout>
</template>

<script>
import axios from 'axios';
import moment from 'moment';

import {
    BASE_URL,
    EP_WEATHER,
    EP_ONECALL,
    APPID,
    IT,
    METRIC,
} from '@/constants/axios_constants.js';
moment.locale('it');

export default {
    data() {
        return {
            // barra di ricerca
            place: '',
            label: 'Località',
            hint: 'es. "roma,it" (lo stato è opzionale)',
            loading: false,

            // API calls a buon fine
            resultsAvailable: false,

            // meteo corrente
            lat: 'n.d.',
            lon: 'n.d.',
            city: '-',
            country: '-',
            dt: '-',
            description: '-',
            temp: 'n.d.',
            feelsLike: 'n.d',
            humidity: 'n.d.',
            pressure: 'n.d.',
            windSpeed: 'n.d.',
            windDeg: 'n.d.',
            tempMin: 'n.d.',
            tempMax: 'n.d.',
            sunrise: 'n.d.',
            sunset: 'n.d.',
            uvi: 'n.d.',

            // previsioni
            labels: ['SU', 'MO', 'TU', 'WED', 'TH', 'FR', 'SA'],
            time: 0,
            forecast: [
                {
                    day: 'Tuesday',
                    icon: 'mdi-white-balance-sunny',
                    temp: '24\xB0/12\xB0',
                },
                {
                    day: 'Wednesday',
                    icon: 'mdi-white-balance-sunny',
                    temp: '22\xB0/14\xB0',
                },
                {
                    day: 'Thursday',
                    icon: 'mdi-cloud',
                    temp: '25\xB0/15\xB0',
                },
            ],
        };
    },
    methods: {
        handleSearch() {
            // DESCRIZIONE:
            // vengono effettuate 2 chiamate API usando axios in sequenza, la 2a dipende da alcuni dati (lat e lon)
            //  recuperati dalla 1a. Se la 1a chiamata fallisce, le seconda non viene effettuata.
            // La 1a chiamata recupera i dari correnti della località selezionata (temperatura, vento, pressione,...)
            // La seconda serve per recuperare sostanzialmente le previsioni (più altri dati es. UVI),
            // se questa seconda chiamata non dà esito positivo, i dati recuperati con la 1a chiamata
            // vengono comunque visualizzati.

            this.loading = true; // attivo la progress bar

            // località ricercata + api key + lingua + unità di misura
            const weatherParams =
                '?q=' +
                this.place +
                '&appid=' +
                APPID +
                '&lang=' +
                IT +
                '&units=' +
                METRIC;

            axios
                .get(BASE_URL + EP_WEATHER + weatherParams)
                .then((response) => {
                    console.log('SUCCESS 1st axios call');
                    console.log('response:', response);

                    // la prima API call è ok, estraggo i dati dalla response
                    this.extractWeatherData(response);

                    this.place = ''; // resetto la Search bar
                    this.hint = 'es. "roma,it" (lo stato è opzionale)'; // ripristino hint
                    this.resultsAvailable = true;

                    // preparo i parametri per la 2a API call
                    // località ricercata + api key + lingua + unità di misura + esclusioni
                    const oneCallParams =
                        '?lat=' +
                        response.data.coord.lat +
                        '&lon=' +
                        response.data.coord.lon +
                        '&appid=' +
                        APPID +
                        '&lang=' +
                        IT +
                        '&units=' +
                        METRIC +
                        '&exclude=minutely,hourly';

                    return axios.get(BASE_URL + EP_ONECALL + oneCallParams);
                })
                .then((oneCallResponse) => {
                    // la seconda API call è ok, ho le previsioni
                    console.log('SUCCESS 2nd axios call');
                    this.loading = false; // disattivo la progress bar
                    // ho tutti i dati da visualizzare in oneCallResponse
                    this.handleSuccess(oneCallResponse);
                })
                .catch((error) => {
                    this.loading = false; // disattivo la progress bar
                    this.handleError(error);
                });
        },

        extractWeatherData(response) {
            // DESCRIZIONE:
            // valorizza le variabili locali in data() con i dati ricevuti dalle API

            this.lat = response.data.coord.lat;
            this.lon = response.data.coord.lon;
            this.city = response.data.name;
            this.country = response.data.sys.country;
            this.temp = response.data.main.temp.toFixed(1);
            this.description = response.data.weather[0].description;
            this.feelsLike = response.data.main.feels_like.toFixed(1);
            this.pressure = response.data.main.pressure;
            this.humidity = response.data.main.humidity;

            const date = new Date(response.data.dt * 1000).toUTCString(); // UTC time
            // console.log('date UTC/GMT:', date);
            // console.log('timezone:', response.data.timezone);
            const offsetZone = response.data.timezone; // timezone offset in secondi
            // console.log(
            //     'offset in ore rispetto IT :',
            //     (offsetZone - 7200) / 3600
            // );
            this.dt = moment
                .utc(date)
                .add(offsetZone, 's')
                .format('dddd, D MMMM YYYY LT');

            const sunrise = new Date(
                response.data.sys.sunrise * 1000
            ).toUTCString();
            const sunset = new Date(
                response.data.sys.sunset * 1000
            ).toUTCString();
            this.sunrise = moment
                .utc(sunrise)
                .add(offsetZone, 's')
                .format('LT');
            this.sunset = moment.utc(sunset).add(offsetZone, 's').format('LT');

            this.windSpeed = response.data.wind.speed.toFixed(1);
            this.windDeg = response.data.wind.deg.toFixed(0);
        },

        handleSuccess(response) {
            // DESCRIZIONE:
            // viene chiamata se la 2a chiamata API ha successo

            this.uvi = response.data.current.uvi;
            this.tempMin = response.data.daily[0].temp.min.toFixed(1);
            this.tempMax = response.data.daily[0].temp.max.toFixed(1);

            console.log('oneCallResponse:', response);
        },
        handleError(error) {
            // DESCRIZIONE:
            // gestisce i 2 casi a seconda che fallisca la 1a o la 2a chiamata API
            // NOTA: se la 1a fallisce la 2a non viene neanche effettuata
            // in base a cioò che contiene l'url, stbilisco quale chiamata ha generato errore

            // console.log('error.config', error.config);
            // console.log('error.config.url', error.config.url);

            if (error.config.url.includes('/weather?')) {
                // località non trovata (weather endpoint)
                this.hint = 'Località non trovata!';
            } else {
                // problemi a recuperare le previsioni (oneCall endpoint)
                this.hint = 'ATTENZIONE: alcuni dati non sono disponibili!';
            }
        },
    },
};
</script>
