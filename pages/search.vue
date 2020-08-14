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

                <!-- contenitore di tutti i dati da visualizzare in pagina (correnti e previsioni) -->
                <div v-if="currentDataAvailable">
                    <v-list-item three-line>
                        <v-list-item-content>
                            <v-list-item-subtitle>
                                <v-img
                                    class="d-inline-block flag"
                                    :src="
                                        currentData.flag
                                            ? currentData.flag
                                            : require('../assets/images/flags/united-nations.svg')
                                    "
                                    alt="country flag"
                                    width="30"
                                ></v-img
                                ><span class="text-caption"
                                    >&nbsp;&nbsp; LAT {{ currentData.lat }}&deg;
                                    &nbsp;LON {{ currentData.lon }}&deg;</span
                                >
                            </v-list-item-subtitle>
                            <v-list-item-title class="headline text-wrap">
                                <strong>{{ currentData.city }}</strong
                                >, {{ currentData.country }}
                            </v-list-item-title>
                            <v-list-item-subtitle
                                >{{ currentData.dt }}
                            </v-list-item-subtitle>
                            <v-list-item-subtitle>
                                <strong class="text-h5">{{
                                    currentData.description
                                }}</strong>
                            </v-list-item-subtitle>
                        </v-list-item-content>
                    </v-list-item>

                    <v-card-text>
                        <v-row align="center">
                            <v-col class="text-right" cols="6">
                                <span class="display-1"
                                    ><v-icon>mdi-thermometer</v-icon
                                    >{{ currentData.temp }}&deg;C</span
                                ><br />
                                <span class="text-subtitle-2 grey--text"
                                    >percepita
                                    {{ currentData.feelsLike }}&deg;C</span
                                >
                            </v-col>
                            <v-col cols="6" class="">
                                <v-img
                                    :class="
                                        currentData.iconCode == '01d'
                                            ? 'weather-sunny'
                                            : 'weather-cloudy'
                                    "
                                    :src="currentData.iconPath"
                                    alt="weather icon"
                                    width="92"
                                ></v-img>
                            </v-col>
                        </v-row>
                    </v-card-text>

                    <v-list-item>
                        <v-list-item-content>
                            <v-list-item-subtitle
                                ><v-icon>mdi-weather-windy</v-icon
                                >&nbsp;velocità
                                {{ currentData.windSpeed }}&nbsp;
                                <v-icon>mdi-compass-outline</v-icon
                                >&nbsp;direzione
                                {{ currentData.windDeg }}</v-list-item-subtitle
                            >

                            <v-list-item-subtitle
                                ><v-icon>mdi-water-percent</v-icon>&nbsp;umidità
                                {{ currentData.humidity }}</v-list-item-subtitle
                            >

                            <v-list-item-subtitle
                                ><v-icon>mdi-format-line-weight</v-icon
                                >&nbsp;pressione
                                {{ currentData.pressure }}</v-list-item-subtitle
                            >

                            <v-list-item-subtitle
                                ><v-icon>mdi-weather-sunset-up</v-icon>&nbsp;il
                                sole sorge alle
                                {{ currentData.sunrise }}
                            </v-list-item-subtitle>

                            <v-list-item-subtitle
                                ><v-icon>mdi-weather-sunset-down</v-icon
                                >&nbsp;il sole tramonta alle
                                {{ currentData.sunset }}
                            </v-list-item-subtitle>
                        </v-list-item-content>
                    </v-list-item>

                    <!-- pannello Previsioni -->
                    <div v-if="forecastsAvailable">
                        <h3 class="mt-5">Previsioni</h3>

                        <v-expansion-panels
                            v-model="openedPanel"
                            class="pb-5"
                            accordion
                            flat
                            hover
                        >
                            <v-expansion-panel
                                v-for="(forecast, i) in forecasts"
                                :key="i"
                                class=""
                            >
                                <v-expansion-panel-header
                                    class="py-0 pl-0 pr-4 text-center forecast-header"
                                >
                                    <v-list-item class="">
                                        <v-list-item-title>{{
                                            forecast.weekday
                                        }}</v-list-item-title>

                                        <v-list-item-avatar>
                                            <v-img
                                                :class="
                                                    forecast.iconCode == '01d'
                                                        ? 'weather-sunny'
                                                        : 'weather-cloudy'
                                                "
                                                :src="forecast.iconPath"
                                                alt="forecast icon"
                                                width="46"
                                            ></v-img>
                                        </v-list-item-avatar>

                                        <v-list-item-subtitle class="">
                                            <span
                                                class="text-subtitle-2 blue--text"
                                                >{{
                                                    forecast.tempMin
                                                }}&nbsp;&nbsp;
                                            </span>
                                            <span
                                                class="text-subtitle-2 red--text"
                                            >
                                                {{ forecast.tempMax }}</span
                                            >
                                        </v-list-item-subtitle>
                                    </v-list-item>
                                </v-expansion-panel-header>

                                <v-expansion-panel-content
                                    color="cyan lighten-4"
                                    class="pt-5"
                                >
                                    <h5>
                                        <strong>{{ forecast.date }}</strong>
                                    </h5>
                                    <v-list-item>
                                        <v-list-item-content>
                                            <v-list-item-subtitle>
                                                <v-icon
                                                    >mdi-text-box-outline</v-icon
                                                >&nbsp;
                                                {{ forecast.description }}
                                            </v-list-item-subtitle>

                                            <v-list-item-subtitle
                                                ><v-icon
                                                    >mdi-weather-windy</v-icon
                                                >&nbsp;
                                                {{
                                                    forecast.windSpeed
                                                }}&nbsp;&nbsp;
                                                <v-icon
                                                    >mdi-compass-outline</v-icon
                                                >&nbsp;
                                                {{
                                                    forecast.windDeg
                                                }}</v-list-item-subtitle
                                            >

                                            <v-list-item-subtitle
                                                ><v-icon
                                                    >mdi-water-percent</v-icon
                                                >&nbsp; {{ forecast.humidity }}
                                                &nbsp;&nbsp;
                                                <v-icon
                                                    >mdi-format-line-weight</v-icon
                                                >&nbsp; {{ forecast.pressure }}
                                            </v-list-item-subtitle>

                                            <v-list-item-subtitle
                                                ><v-icon>mdi-sunglasses</v-icon
                                                >&nbsp;&nbsp;UVI (ore 12.00)
                                                {{ forecast.uvi }}
                                            </v-list-item-subtitle>

                                            <v-list-item-subtitle
                                                ><v-icon
                                                    >mdi-weather-sunset-up</v-icon
                                                >&nbsp;alba
                                                {{ forecast.sunrise }}
                                                &nbsp;&nbsp;
                                                <v-icon
                                                    >mdi-weather-sunset-down</v-icon
                                                >&nbsp;tramonto
                                                {{ forecast.sunset }}
                                            </v-list-item-subtitle>

                                            <v-list-item-subtitle>
                                                <v-icon
                                                    >mdi-umbrella-outline</v-icon
                                                >&nbsp;probabilità
                                                {{ forecast.pop }}
                                                <span
                                                    v-if="forecast.pop != '0%'"
                                                    >&nbsp;&nbsp;<v-icon
                                                        >mdi-water-outline</v-icon
                                                    >{{ forecast.rain }}</span
                                                >
                                            </v-list-item-subtitle>
                                        </v-list-item-content>
                                    </v-list-item>
                                </v-expansion-panel-content>
                            </v-expansion-panel>
                        </v-expansion-panels>
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
            forecastsAvailable: true,

            // meteo corrente
            currentData: {},

            // previsioni
            forecasts: [],

            // accordion opened panels
            openedPanel: null,
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
            this.openedPanel = null; // chiudo eventuali pannelli dell'accordion aperti

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
                    // console.log('SUCCESS 1st axios call');
                    // console.log('response:', response);

                    this.place = ''; // svuoto la Search bar
                    this.hint = 'es. "roma,it" (lo stato è opzionale)'; // ripristino hint al msg iniziale
                    this.currentDataAvailable = true; // ho disponibili i dati del meteo corrente
                    this.currentData = {}; // resetto dati meteo correnti
                    this.forecasts = []; // resetto dati previsioni

                    // la prima API call è ok, estraggo i dati dalla response
                    this.extractWeatherData(response.data);

                    // preparo i parametri per la 2a API call
                    // località ricercata + api key + lingua + unità di misura + esclusioni
                    const oneCallParams =
                        '?lat=' +
                        this.currentData.lat +
                        '&lon=' +
                        this.currentData.lon +
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
                    // console.log('SUCCESS 2nd axios call');
                    this.loading = false; // disattivo la progress bar
                    this.forecastsAvailable = true; // ho disponibili i dati delle previsioni

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
            // valorizza l'oggetto "currentData" (meteo corrente) in data() con i dati ricevuti dalle API

            this.currentData.lat = data.coord.lat;
            this.currentData.lon = data.coord.lon;
            this.currentData.city = data.name;
            this.currentData.country = data.sys.country;

            this.currentData.flag = require('../assets/images/flags/' +
                this.currentData.country.toLowerCase() +
                '.svg');

            this.currentData.temp = Math.round(data.main.temp);
            this.currentData.feelsLike = Math.round(data.main.feels_like);
            this.currentData.description = data.weather[0].description;
            this.currentData.iconCode = data.weather[0].icon;
            this.currentData.iconPath =
                'http://openweathermap.org/img/wn/' +
                data.weather[0].icon +
                '@2x.png';
            this.currentData.pressure = data.main.pressure + 'hPa';
            this.currentData.humidity = data.main.humidity + '%';

            const date = new Date(data.dt * 1000).toUTCString(); // UTC time
            const offsetZone = data.timezone; // timezone offset in secondi
            this.currentData.dt = moment
                .utc(date)
                .add(offsetZone, 's')
                .format('dddd, D MMMM YYYY LT');

            const sunrise = new Date(data.sys.sunrise * 1000).toUTCString();
            const sunset = new Date(data.sys.sunset * 1000).toUTCString();
            this.currentData.sunrise = moment
                .utc(sunrise)
                .add(offsetZone, 's')
                .format('LT');
            this.currentData.sunset = moment
                .utc(sunset)
                .add(offsetZone, 's')
                .format('LT');

            this.currentData.windSpeed = data.wind.speed.toFixed(1) + 'm/s';
            this.currentData.windDeg = data.wind.deg.toFixed(0) + '\xB0';

            // console.log('currentData:', this.currentData);
        },

        extractOnecallData(data) {
            // DESCRIZIONE:
            // viene chiamata se la 2a chiamata API ha successo
            // valorizza l'array "forecasts" in data() con i dati ricevuti dalle API

            // console.log('oneCallResponse:', data);

            // previsioni
            const forecastsQty = data.daily.length;
            const offsetZone = data.timezone_offset; // timezone offset in secondi

            for (let i = 1; i <= forecastsQty - 1; i++) {
                const forecastDate = new Date(
                    data.daily[i].dt * 1000
                ).toUTCString(); // UTC time

                const forecastObj = {
                    date: moment
                        .utc(forecastDate)
                        .add(offsetZone, 's')
                        .format('D MMMM YYYY'),
                    weekday: moment
                        .utc(forecastDate)
                        .add(offsetZone, 's')
                        .format('dddd'),
                    iconCode: data.daily[i].weather[0].icon,
                    iconPath:
                        'http://openweathermap.org/img/wn/' +
                        data.daily[i].weather[0].icon +
                        '@2x.png',
                    tempMin: Math.round(data.daily[i].temp.min) + '\xB0',
                    tempMax: Math.round(data.daily[i].temp.max) + '\xB0',

                    // dettagli previsioni (accordion)
                    description: data.daily[i].weather[0].description,

                    // tempMorning: Math.round(data.daily[i].temp.morn) + '\xB0',
                    // tempNoon: Math.round(data.daily[i].temp.day) + '\xB0',
                    // tempEvening: Math.round(data.daily[i].temp.eve) + '\xB0',
                    // tempNight: Math.round(data.daily[i].temp.night) + '\xB0',

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

                    humidity: data.daily[i].humidity + '%',
                    pressure: data.daily[i].pressure + 'hPa',
                    pop: Math.round(data.daily[i].pop * 100) + '%',
                    rain:
                        typeof data.daily[i].rain !== 'undefined'
                            ? data.daily[i].rain + 'mm'
                            : 'n.d.',
                    windDeg: data.daily[i].wind_deg + '\xB0',
                    windSpeed: data.daily[i].wind_speed.toFixed(1) + 'm/s',
                };

                this.forecasts.push(forecastObj);
            }
            // console.log('forecasts:', this.forecasts);
        },

        handleError(error) {
            // DESCRIZIONE:
            // gestisce i 2 casi a seconda che fallisca la 1a o la 2a chiamata API
            // NOTA: se la 1a fallisce la 2a non viene neanche effettuata
            // in base a cioò che contiene l'url, stbilisco quale chiamata ha generato errore

            // console.log('error.config', error.config);
            // console.log('error.config.url', error.config.url);

            // console.log('ERROR an axios call failed');

            if (error.config.url.includes('/weather?')) {
                // località non trovata (weather endpoint)
                this.hint = 'Località non trovata!';
            } else {
                // problemi a recuperare le previsioni (oneCall endpoint)
                this.hint = 'ATTENZIONE: previsioni non disponibili!';
                this.forecastsAvailable = false;
            }
        },
    },
};
</script>

<style lang="scss" scoped>
.flag {
    vertical-align: middle;
}

.weather-cloudy {
    filter: drop-shadow(0 0 5px gray);
}
.weather-sunny {
    filter: drop-shadow(0 0 5px $yellow-accent-2);
}
.forecast-header {
    height: 38px;
    min-height: 38px;
    max-height: 38px;
    background-color: $cyan-lighten-5;
}
.v-expansion-panel {
    max-width: 95%;
}

::v-deep .v-expansion-panel-content .v-expansion-panel-content__wrap {
    padding: 0 0 16px;
}

::v-deep .v-expansion-panel-header--active {
    background-color: $cyan-lighten-3;
    border-top-left-radius: 4px;
    border-top-right-radius: 4px;
}
</style>
