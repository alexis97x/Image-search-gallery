<script setup>

    import {ref} from 'vue'
    import axios from 'axios'

    const search = ref('')
    const images = ref([])
    const text = ref('URL copied to clipboard!')
    const snackbar = ref(false)
    const noResults = ref(false)
    const client_id = //your token or client_id or access key idk what its called

    const searchImage = () => {
        axios.get(`https://api.unsplash.com/search/photos?page=1&query=${search.value}&client_id=${client_id}`)
        .then(response => {
            if(response.data.results.length <= 0) {
                noResults.value = true
                console.log('no results should show up')
            }
            else {
                noResults.value = false
                images.value = response.data.results
            }
        })
        search.value = ''
    }

    function copyUrl(url) {
        navigator.clipboard.writeText(url)
        snackbar.value = true
    }

</script>
<template>
    <div>
        <v-container>
            <v-text-field
            v-model="search"
            label="Search an image..."
            @keydown.enter="searchImage"
            >

            <template v-slot:append-inner>
                <v-btn size="x-small" icon="mdi-magnify" @click="searchImage"></v-btn>
            </template>

            </v-text-field>
            <v-row>
                <v-col cols="12" v-show="noResults" class="text-center">No results found :3 </v-col>
                <v-col cols="3" v-for="img in images" :key="img" v-show="!noResults">
                    <v-hover v-slot="{isHovering, props}">
                        <v-card @click="copyUrl(img.urls.raw)" :elevation="isHovering ? 10 : 0" :class="{'on-hover': isHovering}" v-bind="props">
                            <v-img :src="img.urls.raw">
                                <template v-slot:placeholder>
                                    <v-row
                                    class="fill-height"
                                    justify="center"
                                    align="center">
                                        <v-progress-circular
                                        :size="70"
                                        :width="7"
                                        color="purple"
                                        indeterminate
                                        ></v-progress-circular>
                                    </v-row>
                                </template>
                            </v-img>
                        </v-card>
                    </v-hover>
                </v-col>
            </v-row>
        </v-container>

        <v-snackbar
            v-model="snackbar">
            {{ text }}
            <template v-slot:actions>
                <v-btn color="pink" variant="text" @click="snackbar = false">Close</v-btn>
            </template>
        </v-snackbar>

    </div>
</template>