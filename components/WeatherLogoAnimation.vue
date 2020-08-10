<template>
    <img class="wr_svg my-3 mx-3" alt="weather image" src="/weather.svg" />
</template>

<style>
.wr_svg {
    height: 180px;
    width: 180px;
    transform: rotateY(560deg);
    animation: turn 3.5s ease-out forwards 1s;
}

@keyframes turn {
    100% {
        transform: rotateY(0deg);
    }
}

/* riduco dimensione del logo su mobile */
@media screen and (max-width: 460px) {
    .wr_svg {
        height: 140px;
        width: 140px;
    }
}
</style>
