<template>
    <Page>
        <ActionBar title="3-WORDS"/>
        <FlexboxLayout flexDirection="column" backgroundColor="#3c495e">
            <Label :text="`Latitude: ${latitude}`" height="auto" />
            <Label :text="`Longitude: ${longitude}`" height="auto" />
            <Label v-for="word in words" :key="word" :text="word" class="word" />
        </FlexboxLayout>
    </Page>
</template>

<script>
    // https://github.com/NativeScript/nativescript-geolocation
    // https://www.thepolyglotdeveloper.com/2017/03/device-geolocation-nativescript-angular-application/
    // https://code.tutsplus.com/tutorials/code-a-real-time-nativescript-app-geolocation-and-google-maps--cms-29001
    import * as Geolocation from "nativescript-geolocation";
    import { Accuracy } from "tns-core-modules/ui/enums";

    const locationOptions = {
        desiredAccuracy: Accuracy.high, 
        updateTime: 1000,
        minimumUpdateTime: 1000,
        updateDistance: 1
    };

    const what3wordsKey = 'OQGKETD3';

    export default {
        data() {
            return {
                latitude: '',
                longitude: '',
                words: []
            };
        },
        methods: {
            watchLocation () {
                Geolocation.watchLocation(location => {
                    this.latitude = location.latitude;
                    this.longitude = location.longitude;
                    this.fetchWords(location);
                }, error => {
                    throw new Error(error);
                }, locationOptions);
            },
            fetchWords (location) {
                fetch(`https://api.what3words.com/v2/reverse?coords=${location.latitude},${location.longitude}&key=${what3wordsKey}&lang=en&display=minimal`)
                .then((response) => response.json())
                .then((r) => { this.words = r.words.split('.'); });
            }
        },
        mounted () {
            Geolocation.isEnabled().then(available => {
                if (available) {
                    this.watchLocation();
                } else {
                    console.log('Location services not enabled');
                }
            });
        }
    };
</script>

<style scoped>
    ActionBar {
        background-color: #53ba82;
        color: #ffffff;
        text-align: center;
    }
    FlexboxLayout {
        padding: 20px;
    }
    Label {
        vertical-align: center;
        font-size: 20px;
        color: #ffffff;
    }
    .word {
        font-size: 60px;
        text-align: center;
    }
</style>
