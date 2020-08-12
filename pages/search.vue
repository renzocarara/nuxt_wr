<template>
    <v-layout>
        <v-flex class="text-center">
            <v-card class="mx-auto" max-width="400" color="cyan lighten-5">
                <!-- Search Bar -->
                <v-text-field
                    v-model="place"
                    class="px-10 py-10"
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

                <!-- lista che contiene tutti i dati da visualizzare in pagina -->
                <div v-if="currentDataAvailable">
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
                                <span class="display-1">{{ temp }}&deg;C</span
                                ><br />
                                <span class="text-subtitle-2 grey--text"
                                    >percepita {{ feelsLike }}&deg;C</span
                                >
                            </v-col>
                            <v-col cols="6" class="">
                                <v-img
                                    class=""
                                    :src="icon"
                                    alt="weather icon"
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

                            <!-- <v-list-item-subtitle
                                ><v-icon>mdi-sunglasses</v-icon>&nbsp;indice UV
                                {{ uvi }}
                            </v-list-item-subtitle> -->

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

                    <div v-if="forecastAvailable">
                        <h3 class="mt-5">Previsioni</h3>

                        <v-list class="transparent">
                            <v-list-item
                                v-for="item in forecast"
                                :key="item.weekday"
                                class="forecast-list"
                            >
                                <v-list-item-title>{{
                                    item.weekday
                                }}</v-list-item-title>

                                <v-list-item-avatar>
                                    <v-img
                                        :src="item.icon"
                                        alt="forecast icon"
                                        width="46"
                                    ></v-img>
                                </v-list-item-avatar>

                                <v-list-item-subtitle class="">
                                    <span class="text-subtitle-2 blue--text"
                                        >{{ item.tempMin }}&nbsp;&nbsp;
                                    </span>
                                    <span class="text-subtitle-2 red--text">
                                        {{ item.tempMax }}</span
                                    >
                                </v-list-item-subtitle>
                            </v-list-item>
                        </v-list>

                        <h4 class="mt-5">Dettagli</h4>

                        <v-slider
                            v-model="dayTick"
                            :max="6"
                            :tick-labels="labels"
                            class="mx-4"
                            ticks
                        ></v-slider>
                    </div>
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

            // esiti chiamate API
            currentDataAvailable: false,
            forecastAvailable: true,

            // meteo corrente
            lat: 'n.d.',
            lon: 'n.d.',
            city: '-',
            country: '-',
            dt: '-',
            description: '-',
            icon: '@/assets/images/icons/00@2x.png',
            temp: 'n.d.',
            feelsLike: 'n.d',
            humidity: 'n.d.',
            pressure: 'n.d.',
            windSpeed: 'n.d.',
            windDeg: 'n.d.',
            sunrise: 'n.d.',
            sunset: 'n.d.',

            // previsioni
            forecast: [],

            labels: ['SU', 'MO', 'TU', 'WED', 'TH', 'FR', 'SA'],
            dayTick: 0,
        };
    },

    methods: {
        handleSearch() {
            // DESCRIZIONE:
            // vengono effettuate 2 chiamate API usando axios in sequenza, la 2a dipende da alcuni dati (lat e lon)
            //  recuperati dalla 1a. Se la 1a chiamata fallisce, le seconda non viene effettuata.
            // La 1a chiamata recupera i dari correnti della località selezionata (temperatura, vento, pressione,...)
            // La seconda serve per recuperare sostanzialmente le previsioni,
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
                    this.extractWeatherData(response.data);

                    // creare una function di reset, da chiamare prima di estrarre i dati <<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<

                    this.place = ''; // resetto la Search bar
                    this.hint = 'es. "roma,it" (lo stato è opzionale)'; // ripristino hint
                    this.currentDataAvailable = true;
                    this.forecast = [];
                    this.forecastAvailable = true;

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
                    this.extractOnecallData(oneCallResponse.data);
                })
                .catch((error) => {
                    this.loading = false; // disattivo la progress bar
                    this.handleError(error);
                });
        },

        extractWeatherData(data) {
            // DESCRIZIONE:
            // valorizza le variabili locali in data() con i dati ricevuti dalle API

            this.lat = data.coord.lat;
            this.lon = data.coord.lon;
            this.city = data.name;
            this.country = data.sys.country;
            this.temp = Math.round(data.main.temp);
            this.feelsLike = Math.round(data.main.feels_like);
            this.description = data.weather[0].description;
            this.icon =
                'http://openweathermap.org/img/wn/' +
                data.weather[0].icon +
                '@2x.png';
            this.pressure = data.main.pressure;
            this.humidity = data.main.humidity;

            const date = new Date(data.dt * 1000).toUTCString(); // UTC time
            const offsetZone = data.timezone; // timezone offset in secondi
            this.dt = moment
                .utc(date)
                .add(offsetZone, 's')
                .format('dddd, D MMMM YYYY LT');

            const sunrise = new Date(data.sys.sunrise * 1000).toUTCString();
            const sunset = new Date(data.sys.sunset * 1000).toUTCString();
            this.sunrise = moment
                .utc(sunrise)
                .add(offsetZone, 's')
                .format('LT');
            this.sunset = moment.utc(sunset).add(offsetZone, 's').format('LT');

            this.windSpeed = data.wind.speed.toFixed(1);
            this.windDeg = data.wind.deg.toFixed(0);
        },

        extractOnecallData(data) {
            // DESCRIZIONE:
            // viene chiamata se la 2a chiamata API ha successo
            // valorizza le variabili locali in data() con i dati ricevuti dalle API

            console.log('oneCallResponse:', data);

            // previsioni
            const forecastQty = data.daily.length;
            const offsetZone = data.timezone_offset; // timezone offset in secondi

            for (let i = 1; i <= forecastQty - 1; i++) {
                const forecastDate = new Date(
                    data.daily[i].dt * 1000
                ).toUTCString(); // UTC time

                const forecastObj = {
                    id: i - 1,
                    weekday: moment
                        .utc(forecastDate)
                        .add(offsetZone, 's')
                        .format('dddd'),
                    icon:
                        'http://openweathermap.org/img/wn/' +
                        data.daily[i].weather[0].icon +
                        '@2x.png',
                    tempMin: Math.round(data.daily[i].temp.min),
                    tempMax: Math.round(data.daily[i].temp.max),

                    // dettagli previsioni
                    description: data.daily[i].weather[0].description,

                    tempMorning: Math.round(data.daily[i].temp.morn),
                    tempNoon: Math.round(data.daily[i].temp.day),
                    tempEvening: Math.round(data.daily[i].temp.eve),
                    tempNight: Math.round(data.daily[i].temp.night),

                    uvi: data.daily[i].uvi,

                    sunrise: moment
                        .utc(
                            new Date(data.daily[i].sunrise * 1000).toUTCString()
                        )
                        .add(offsetZone, 's')
                        .format('LT'),
                    sunset: moment
                        .utc(
                            new Date(data.daily[i].sunset * 1000).toUTCString()
                        )
                        .add(offsetZone, 's')
                        .format('LT'),

                    humidity: data.daily[i].humidity,
                    pressure: data.daily[i].pressure,
                    pop: data.daily[i].pop * 100,
                    rain: data.daily[i].rain,
                };

                this.forecast.push(forecastObj);
            }
            console.log('forecast:', this.forecast);
        },

        handleError(error) {
            // DESCRIZIONE:
            // gestisce i 2 casi a seconda che fallisca la 1a o la 2a chiamata API
            // NOTA: se la 1a fallisce la 2a non viene neanche effettuata
            // in base a cioò che contiene l'url, stbilisco quale chiamata ha generato errore

            // console.log('error.config', error.config);
            // console.log('error.config.url', error.config.url);

            console.log('ERROR axios call failed');

            if (error.config.url.includes('/weather?')) {
                // località non trovata (weather endpoint)
                this.hint = 'Località non trovata!';
            } else {
                // problemi a recuperare le previsioni (oneCall endpoint)
                this.hint = 'ATTENZIONE: previsioni non disponibili!';
                this.forecastAvailable = false;
            }
        },
    },
};
</script>

<style lang="scss" scoped>
.forecast-list {
    height: 38px;
    min-height: 38px;
}
</style>
