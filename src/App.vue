<template>
    <v-app>
        <v-app-bar
            app
            color="#3577E1"
            dark
        >
            <div class="d-flex align-center">
                <v-img
                    alt="Amkor"
                    class="shrink mr-1"
                    contain
                    src="img/Amkor_Technology_logo.svg"
                    transition="scale-transition"
                    height="50"
                />

            </div>

            <v-spacer></v-spacer>

            <v-btn
                href="https://amkorpwa.glitch.me"
                target="_blank"
                text
            >
                <span class="mr-2">Online Version</span>
                <v-icon>mdi-open-in-new</v-icon>
            </v-btn>
        </v-app-bar>

        <v-content>

            <v-container id="container">
                <router-view></router-view>
            </v-container>

        </v-content>
    </v-app>
</template>

<script>
//
</script>
