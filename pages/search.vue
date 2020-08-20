<template>
    <v-layout>
        <v-flex class="text-center">
            <v-card class="mx-auto" max-width="400" color="cyan lighten-5">
                <!-- Search Bar -->
                <v-text-field
                    id="place-input"
                    v-model="place"
                    class="px-10 pt-10 pb-5"
                    placeholder="Inserisci la località..."
                    clearable
                    persistent-hint
                    :label="label"
                    :hint="hint"
                    :loading="loading"
                    loader-height="3"
                    :error="error"
                    append-icon="mdi-magnify"
                    @click:append="checkInput"
                    @keypress.13="checkInput"
                ></v-text-field>

                <!-- contenitore di tutti i dati da visualizzare in pagina (correnti e previsioni) -->
                <div v-if="currentDataAvailable">
                    <v-list-item three-line>
                        <v-list-item-content class="pt-0">
                            <!-- latitudine e longitudine -->
                            <v-list-item-subtitle>
                                <v-icon>mdi-crosshairs-gps</v-icon>
                                <span class="text-caption"
                                    >LAT {{ currentData.lat }}&deg; &nbsp;LON
                                    {{ currentData.lon }}&deg;</span
                                >
                            </v-list-item-subtitle>

                            <!-- bandiera, nome località, codice stato -->
                            <v-list-item-title class="headline text-wrap">
                                <img
                                    class="d-inline-block flag"
                                    :src="
                                        currentData.flag
                                            ? currentData.flag
                                            : require('../assets/images/flags/united-nations.svg')
                                    "
                                    alt="country
                                flag"
                                /><img /> <strong>{{ currentData.city }}</strong
                                >, {{ currentData.country }}

                                <!-- stellina per settare/resettare la località preferita -->
                                <v-checkbox
                                    v-if="browserHasStorage"
                                    v-model="isPreferredPlace"
                                    dense
                                    color="red accent-4"
                                    off-icon="mdi-star-outline"
                                    on-icon="mdi-star"
                                    hide-details="true"
                                    class="d-inline-block my-0"
                                    @change="updateStorage"
                                ></v-checkbox>

                                <!-- snackbar di avviso quando viene checkata/uncheckata la stellina -->
                                <v-snackbar
                                    v-model="snackbar"
                                    :timeout="timeout"
                                    color="red accent-4"
                                    centered
                                    elevation="10"
                                >
                                    {{ snackText }}
                                </v-snackbar>
                            </v-list-item-title>

                            <!-- data: weekday, gg mm aa hh:mm -->
                            <v-list-item-subtitle class="text-subtitle-1"
                                >{{ currentData.dt }}
                            </v-list-item-subtitle>

                            <!-- descrizione del meteo corrente -->
                            <v-list-item-subtitle>
                                <strong class="text-h5">{{
                                    currentData.description
                                }}</strong>
                            </v-list-item-subtitle>
                        </v-list-item-content>
                    </v-list-item>

                    <v-card-text class="py-0">
                        <v-row align="center">
                            <v-col class="text-right" cols="6">
                                <span class="display-1"
                                    ><v-icon>mdi-thermometer</v-icon
                                    >{{ currentData.temp }}&deg;C</span
                                ><br />
                                <span class="text-subtitle-1 grey--text"
                                    >percepita
                                    {{ currentData.feelsLike }}&deg;C</span
                                >
                            </v-col>
                            <v-col cols="6" class="py-0">
                                <v-img
                                    :class="
                                        currentData.iconCode == '01d'
                                            ? 'weather-sunny'
                                            : 'weather-cloudy'
                                    "
                                    :src="currentData.iconPath"
                                    alt="weather icon"
                                    width="102"
                                ></v-img>
                            </v-col>
                        </v-row>
                    </v-card-text>

                    <v-list-item>
                        <v-list-item-content>
                            <v-list-item-subtitle class="text-subtitle-1"
                                ><v-icon>mdi-weather-windy</v-icon
                                >&nbsp;velocità {{ currentData.windSpeed
                                }}<br />
                                <v-icon>mdi-compass-outline</v-icon
                                >&nbsp;direzione
                                {{ currentData.windDeg }}</v-list-item-subtitle
                            >

                            <v-list-item-subtitle class="text-subtitle-1"
                                ><v-icon>mdi-water-percent</v-icon>&nbsp;umidità
                                {{ currentData.humidity }}</v-list-item-subtitle
                            >

                            <v-list-item-subtitle class="text-subtitle-1"
                                ><v-icon>mdi-format-line-weight</v-icon
                                >&nbsp;pressione
                                {{ currentData.pressure }}</v-list-item-subtitle
                            >

                            <v-list-item-subtitle class="text-subtitle-1"
                                ><v-icon>mdi-weather-sunset-up</v-icon>&nbsp;il
                                sole sorge alle
                                {{ currentData.sunrise }}
                            </v-list-item-subtitle>

                            <v-list-item-subtitle class="text-subtitle-1"
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
                                <!-- header: weekday, iconcina meteo, temp min e temp max -->
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
                                                class="text-subtitle-1 blue--text"
                                                >{{
                                                    forecast.tempMin
                                                }}&nbsp;&nbsp;
                                            </span>
                                            <span
                                                class="text-subtitle-1 red--text"
                                            >
                                                {{ forecast.tempMax }}</span
                                            >
                                        </v-list-item-subtitle>
                                    </v-list-item>
                                </v-expansion-panel-header>

                                <!-- dettagli previsioni del sinolo giorno -->
                                <v-expansion-panel-content
                                    color="cyan lighten-4"
                                    class="pt-5"
                                >
                                    <!-- gg mm aaaa -->
                                    <h4>
                                        <strong>{{ forecast.date }}</strong>
                                    </h4>
                                    <v-list-item>
                                        <v-list-item-content>
                                            <v-list-item-subtitle
                                                class="text-subtitle-1"
                                            >
                                                <v-icon
                                                    >mdi-text-box-outline</v-icon
                                                >&nbsp;
                                                {{ forecast.description }}
                                            </v-list-item-subtitle>

                                            <v-list-item-subtitle
                                                class="text-subtitle-1"
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
                                                class="text-subtitle-1"
                                                ><v-icon
                                                    >mdi-water-percent</v-icon
                                                >&nbsp; {{ forecast.humidity }}
                                                &nbsp;&nbsp;
                                                <v-icon
                                                    >mdi-format-line-weight</v-icon
                                                >&nbsp; {{ forecast.pressure }}
                                            </v-list-item-subtitle>

                                            <v-list-item-subtitle
                                                class="text-subtitle-1"
                                                ><v-icon>mdi-sunglasses</v-icon
                                                >&nbsp;&nbsp;UVI
                                                <v-chip
                                                    class="uvi-button"
                                                    small
                                                    :color="forecast.uvicolor"
                                                    >{{ forecast.uvi }}</v-chip
                                                >&nbsp;&nbsp;

                                                <!-- bottoncino "informazioni" su UVI -->
                                                <!-- color="red accent-4" -->
                                                <v-btn
                                                    color="cyan lighten-4"
                                                    class="px-0"
                                                    elevation="5"
                                                    x-small
                                                    @click.stop="dialog = true"
                                                >
                                                    <v-icon class="info-icon"
                                                        >mdi-information-variant</v-icon
                                                    >
                                                </v-btn>
                                                <v-dialog
                                                    v-model="dialog"
                                                    max-width="290"
                                                >
                                                    <v-card
                                                        color="cyan lighten-5"
                                                    >
                                                        <v-card-title
                                                            class="headline"
                                                            >Indice UV
                                                        </v-card-title>

                                                        <v-card-text>
                                                            <v-img
                                                                :src="
                                                                    require('../assets/images/uvipanel.png')
                                                                "
                                                                alt="indici UV"
                                                            ></v-img>
                                                        </v-card-text>

                                                        <v-card-actions>
                                                            <v-spacer></v-spacer>

                                                            <v-btn
                                                                color="red accent-4"
                                                                dark
                                                                target="_blank"
                                                                href="https://www.melanomaitalia.org/trova-le-risposte/prevenzione/le-radiazioni-ultraviolette/indice-ultravioletto/#:~:text=L'indice%20predice%20il%20rischio,%2B%20(rischio%20molto%20alto).&text=Indice%20UV%203%2D5%3A%20significa,in%20meno%20di%2020%20minuti."
                                                            >
                                                                Ulteriori Info
                                                            </v-btn>

                                                            <v-btn
                                                                color="red accent-4"
                                                                dark
                                                                @click="
                                                                    dialog = false
                                                                "
                                                            >
                                                                Chiudi
                                                            </v-btn>
                                                        </v-card-actions>
                                                    </v-card>
                                                </v-dialog>
                                            </v-list-item-subtitle>

                                            <v-list-item-subtitle
                                                class="text-subtitle-1"
                                                ><v-icon
                                                    >mdi-weather-sunset-up</v-icon
                                                >&nbsp;alba
                                                {{ forecast.sunrise }}
                                                <br />
                                                <v-icon
                                                    >mdi-weather-sunset-down</v-icon
                                                >&nbsp;tramonto
                                                {{ forecast.sunset }}
                                            </v-list-item-subtitle>

                                            <v-list-item-subtitle
                                                class="text-subtitle-1"
                                            >
                                                <v-icon
                                                    >mdi-umbrella-outline</v-icon
                                                >&nbsp;probabilità
                                                {{ forecast.pop }}
                                                <br />
                                                <span
                                                    v-if="forecast.pop != '0%'"
                                                    >&nbsp;&nbsp;<v-icon>mdi-water-outline</v-icon>&nbsp;quantità&nbsp;{{
                                                        forecast.rain
                                                    }}</span
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
} from '@/constants/axios_constants.js'; // costanti per le chiamate axios

const hideVirtualKeyboard = require('hide-virtual-keyboard'); //

moment.locale('it'); // localizzazione per "moment"

export default {
    data() {
        return {
            // barra di ricerca
            place: '',
            label: 'Località',
            hint: 'es. "roma,it" (lo stato è opzionale)',
            loading: false,
            error: false, // quando a true applica il colore rosso alla text-field input

            // flag per verificare se il browser supporta Web Storage
            browserHasStorage: false,
            // checkbox per selezione località preferita
            isPreferredPlace: false,

            // snackbar di avviso località preferita
            snackbar: false,
            snackText: '',
            timeout: 2000,

            // esiti chiamate API
            currentDataAvailable: false,
            forecastsAvailable: true,

            // meteo corrente
            currentData: {},

            // previsioni
            forecasts: [],

            // accordion opened panels
            openedPanel: null,

            // info dialog UVI
            dialog: false,
        };
    },

    mounted() {
        // DESCRIZIONE:
        // se la località preferita è definita (cioè salvata nel LocalStorage), al caricamento (mount) del componente "Search",
        // viene fatta partire la chiamata axios per recuperare i dati della località preferita per poi presentarli all'utente
        // i dati vengono presentati all'utente automaticamente, senza che sia stata fatta una ricerca

        if (
            // controllo che il browser supporti Web Storage, che il dato esista e sia diverso da stringa vuota
            // nel qual caso recupero i dati della località preferita, chiamando la getApiData()
            this.browserHasStorage &&
            localStorage.getItem('morganaPreferredPlace') !== null &&
            localStorage.getItem('morganaPreferredPlace') !== ''
        ) {
            // console.log('getApiData by ID, called!');
            const by = 'id';
            this.getApiData(by); // call API weather by City Id
        }
    },

    created() {
        // DESCRIZIONE:
        // al momento della creazione del componnte verifico se il browser in uso supporta Web Storage,
        // e setto un flag per indicarlo
        if (typeof Storage !== 'undefined') {
            this.browserHasStorage = true;
            console.log('BROWSER supporta Storage!!');
        }
    },

    methods: {
        checkInput() {
            // DESCRIZIONE:
            // banalmente controlla che l'input cercato non sia nullo (vuoto) o soli blanks
            // stringhe con blanks iniziali o finali vengono "trimmate" e poi passate alla funzione di ricerca

            if (this.place.trim() !== '') {
                const by = 'q'; // call API weather by City Name
                this.getApiData(by);
            }
        },

        getApiData(by) {
            // DESCRIZIONE:
            // vengono effettuate 2 chiamate API usando axios in sequenza, la 2a dipende da alcuni dati (lat e lon)
            // recuperati dalla 1a. Se la 1a chiamata fallisce, le seconda non viene effettuata.
            // La 1a chiamata recupera i dari correnti della località selezionata (temperatura, vento, pressione,...)
            // La seconda serve per recuperare sostanzialmente le previsioni,
            // se questa seconda chiamata non dà esito positivo, i dati recuperati con la 1a chiamata
            // vengono comunque visualizzati, ma le previsioni non saranno disponibili

            hideVirtualKeyboard(); // nascondo la keybord su mobile

            this.loading = true; // attivo la progress bar
            this.openedPanel = null; // chiudo eventuali pannelli dell'accordion aperti
            this.isPreferredPlace = false; // setto il checkbox (stellina) come "non selezionato"

            // stabilisco il tipo di ricerca, se per nome (che arriva dalla SearchBar) o per ID (che arriva da LocalStorage)
            const searchBy =
                by === 'q'
                    ? '?q=' + this.place
                    : '?id=' + localStorage.getItem('morganaPreferredPlace');

            // località ricercata (per ID o per nome) + api key + lingua + unità di misura
            const weatherParams =
                searchBy +
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

                    this.isPreferredPlace = this.verifyPreferred(); // verifico se la località è quella "preferred"

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
                    // this.loading = false; // disattivo la progress bar
                    this.error = false; // disattivo la condizione di errore
                    this.forecastsAvailable = true; // ho disponibili i dati delle previsioni

                    // ho tutti i dati da visualizzare in oneCallResponse
                    this.extractOnecallData(oneCallResponse.data);
                })

                .catch((error) => {
                    console.log('error', error);
                    // this.loading = false; // disattivo la progress bar
                    this.error = true; // attivo la condizione di errore
                    this.handleError(error);
                })
                .finally(() => {
                    this.loading = false; // disattivo la progress bar
                });
        },

        verifyPreferred() {
            // DESCRIZIONE:
            // ritorna vero se la località appena ricercata è quella salvata come preferita in localStorage
            // altrimenti ritorna falso
            // Nota: l'id memorizzato in localStorage è una "stringa", mentre l'id che arriva dalla API è un "numero"
            // Nota2: se la chiave non è impostata(cioè definita nello Storage), getItem() restituisce "NULL"

            return (
                this.currentData.id.toString() ===
                localStorage.getItem('morganaPreferredPlace')
            );
        },
        updateStorage() {
            // DESCRIZIONE:
            // viene chiamata quando c'e' un click/tap sulla checkbox che indica la località preferita
            // aggiorna il valore della  località preferita in localStorage,
            // se la località era selezionata e viene deselezionata, in localStorage viene salvata una stringa vuota

            this.snackbar = true; // rendo visibile lo snackbar di avviso

            if (this.isPreferredPlace) {
                // salvo la preferenza in LocalStorage
                localStorage.setItem(
                    'morganaPreferredPlace',
                    this.currentData.id
                );

                console.log(
                    'preferenza:',
                    localStorage.getItem('morganaPreferredPlace')
                );

                // setto il testo dello snackbar che appare
                this.snackText = 'Località preferita salvata!';
            } else {
                // preferenza deselezionata, resetto il dato in LocalStorage
                localStorage.setItem('morganaPreferredPlace', '');
                console.log('nessuna preferenza salvata!');

                // setto il testo dello snackbar che appare
                this.snackText = 'Preferenza rimossa!';
            }
        },

        extractWeatherData(data) {
            // DESCRIZIONE:
            // valorizza l'oggetto "currentData" (meteo corrente) in data() con i dati ricevuti dalle API

            this.currentData.lat = data.coord.lat;
            this.currentData.lon = data.coord.lon;
            this.currentData.city = data.name;
            this.currentData.id = data.id; // city id
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

                    uvi: data.daily[i].uvi.toFixed(2),
                    uvicolor: this.setUviColor(data.daily[i].uvi),

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

        setUviColor(uviIndex) {
            let uviColor = '';
            uviIndex = Math.round(uviIndex);
            console.log('uvIndex rounded:', uviIndex);
            if (uviIndex <= 2) {
                // BASSO
                uviColor = 'green';
            } else if (uviIndex <= 5) {
                // MODERATO
                uviColor = 'yellow';
            } else if (uviIndex <= 7) {
                // ALTO
                uviColor = 'orange';
            } else if (uviIndex <= 10) {
                // MOLTO ALTO
                uviColor = 'red';
            } else {
                uviColor = 'purple'; // ESTREMO
            }

            return uviColor;
        },

        uviInfos() {
            console.log('uviInfos called!');
        },

        handleError(error) {
            // DESCRIZIONE:
            // gestisce i 2 casi a seconda che fallisca la 1a o la 2a chiamata API
            // NOTA: se la 1a fallisce la 2a non viene neanche effettuata
            // in base a ciò che contiene l'url, stbilisco quale chiamata ha generato errore
            // console.log('ERROR an axios call failed');

            if (error.config.url.includes('/weather?')) {
                // è fallita la 1a chiamata axios (weather endpoint)
                if (error.response.status === 404) {
                    // località non trovata (weather endpoint)
                    this.hint = 'Località non trovata!';
                } else {
                    this.hint = 'Si è verificato un errore!';
                }
            } else {
                // è fallita la 2a chiamata, quindi la 1a è andata bene
                // problemi a recuperare le previsioni (oneCall endpoint)
                this.hint = 'ATTENZIONE: previsioni non disponibili!';
                this.forecastsAvailable = false;
            }
        },
    },
};
</script>

<style lang="scss" scoped>
// bandierina stato
.flag {
    vertical-align: middle;
    width: 30px;
}

// ombra per tutte le icone tranne il sole
.weather-cloudy {
    filter: drop-shadow(0 0 5px gray);
}
// ombra per l'icona del sole
.weather-sunny {
    filter: drop-shadow(0 0 5px $yellow-accent-2);
}
// headers del pannellino con le previsioni per la settimana
.forecast-header {
    height: 38px;
    min-height: 38px;
    max-height: 38px;
    background-color: $cyan-lighten-5;
}
// pannellino che si apre cliccando sull'header
.v-expansion-panel {
    max-width: 95%;
}

// contenuto quando l'header viene espanso (apro la tendina)
::v-deep .v-expansion-panel-content .v-expansion-panel-content__wrap {
    padding: 0 0 16px;
}

// header del pannellino aperto, con le previsioni per la settimana
::v-deep .v-expansion-panel-header--active {
    background-color: $cyan-lighten-3;
    border-top-left-radius: 4px;
    border-top-right-radius: 4px;
}

// text-field text
::v-deep #place-input {
    font-size: 18px;
}
// text-field label
::v-deep .v-text-field .v-label {
    font-size: 20px;
}
// text field hint
::v-deep .v-messages {
    font-size: 14px;
}

// larghezza dello snackbar per la località preferita
::v-deep .v-snack__wrapper {
    max-width: 250px;
    min-width: 250px;
}
// testo contenuto nello snackbar
::v-deep .v-snack__content {
    text-align: center;
    font-weight: bold;
    font-size: 14px;
}
.uvi-button {
    font-weight: bold;
    font-size: 14px;
}
.info-icon {
    font-size: 22px;
}
</style>
