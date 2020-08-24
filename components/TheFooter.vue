<template>
    <v-footer :absolute="!fixed" dark color="cyan lighten-3" app>
        <span>
            <a
                class="link"
                href="https://www.renzocarara.it/"
                target="_blank"
                rel="noopener noreferrer"
                >Renzo Carara - &copy; {{ new Date().getFullYear() }}
            </a></span
        >
        <v-spacer />
        <div v-if="isSearchPage" class="mobile">
            Country flag Icons made by
            <a
                href="https://www.flaticon.com/authors/freepik"
                title="Freepik"
                target="_blank"
                rel="noopener noreferrer"
                >Freepik</a
            >
            from
            <a
                href="https://www.flaticon.com/"
                title="Flaticon"
                target="_blank"
                rel="noopener noreferrer"
                >www.flaticon.com</a
            >
        </div>
    </v-footer>
</template>

<script>
export default {
    data() {
        return {
            fixed: false,
        };
    },

    computed: {
        isSearchPage() {
            // verifico se sono sulla view "search" e ritorno true o false
            console.log('this.$route.name', this.$route.name);
            return this.$route.name === 'search';
        },
    },
};
</script>

<style lang="scss" scoped>
.mobile {
    font-size: 12px;
}
.link {
    text-decoration: none;
    color: white;
}
@media screen and (max-width: 600px) {
    .mobile {
        font-size: 10px;
    }
}
</style>
