<template>
    <v-layout column justify-center align-center>
        <v-flex>
            <v-card color="cyan lighten-5" max-width="800">
                <v-card-title class="headline justify-center">
                    <span
                        ><v-icon size="30" color="red accent-4"
                            >mdi-alert-circle-outline</v-icon
                        ></span
                    >Attenzione
                </v-card-title>
                <v-card-text>
                    <h1 v-if="error.statusCode === 404" class="text-center">
                        <img
                            style="max-width: 100%;"
                            class=""
                            src="@/assets/images/error404.png"
                            alt="computer 404 img"
                        />
                    </h1>
                    <h1 v-else>
                        {{ otherError }}
                    </h1>
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
    layout: 'empty',
    props: {
        error: {
            type: Object,
            default: null,
        },
    },
    data() {
        return {
            pageNotFound: 'Pagina non trovata',
            otherError: 'Si è verificato un errore',
        };
    },
    head() {
        const title =
            this.error.statusCode === 404 ? this.pageNotFound : this.otherError;
        return {
            title,
        };
    },
};
</script>

<style lang="scss" scoped>
h1 {
    font-size: 20px;
}
</style>
