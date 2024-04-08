<script setup>
    import axios from 'axios';
    import {reactive, ref} from 'vue'

    const config = {
        'apiKey': "36593d87e86b050fab77cc27237ec851",
        'apiUrl': "https://api.openweathermap.org/data/2.5/weather?units=metric&lang=vi&q=",
        'iconUrl': 'https://openweathermap.org/img/wn/'
    }

    const isSuccessSearch = ref(false);
    const isFalseSearch = ref(false);
    const cityInput = ref('');
    
    let weather = reactive({
        'icon': '',
        'desc': '',
        'temp': '',
        'name': '',
        'humidity': '',
        'wind': ''
    })

    function searchWeather(city) {
        axios.get(`${config.apiUrl}${city}&appid=${config.apiKey}`)
        .then(response => {
            console.log(response.status);
            isFalseSearch.value = false;
            isSuccessSearch.value = true;
            weather.icon = `${config.iconUrl}${response.data.weather[0].icon}@2x.png`;
            weather.desc = response.data.weather[0].description;
            weather.temp = response.data.main.temp + '°C';
            weather.name = response.data.name;
            weather.humidity = response.data.main.humidity + '%';
            weather.wind = response.data.wind.speed + 'km/h';
        })
        .catch(error => {
            if(error.response.status == 400 || error.response.status == 404) {
                isSuccessSearch.value = false;
                isFalseSearch.value = true;
            }
        });
    }

    function searchWeatherHandler(city) {
        searchWeather(city);
    }
</script>
<template>
    <div>
        <div class="card">
            <div class="search">
                <input v-model="cityInput" type="text" placeholder="Enter city name" @keypress.enter="searchWeatherHandler(cityInput)">
                <button @click="searchWeather(cityInput)">
                    <i class="fa-solid fa-magnifying-glass"></i>
                </button>
            </div>
            <div v-if="isFalseSearch" class="error">
                <p>Invalid city name</p>
            </div>
            <div v-if="isSuccessSearch" class="weather slide">
                <img class="weather-icon" :src="weather.icon">
                <p class="desc">{{ weather.desc }}</p>
                <h1 class="temp">{{ weather.temp }}</h1>
                <h2 class="city">{{ weather.name }}</h2>
                <div class="details">
                    <div class="col">
                        <img src="../assets/humidity.png">
                        <div>
                            <p class="humidity">{{ weather.humidity }}</p>
                            <p>Humidity</p>
                        </div>
                    </div>
                    <div class="col">
                        <img src="../assets/wind.png">
                        <div>
                            <p class="wind">{{ weather.wind }}</p>
                            <p>Wind Speed</p>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>
<style lang="scss">
    $primary: green;
    @import url('https://fonts.googleapis.com/css2?family=Poppins:ital,wght@0,100;0,200;0,300;0,400;0,500;0,600;0,700;0,800;0,900;1,100;1,200;1,300;1,400;1,500;1,600;1,700;1,800;1,900&display=swap');

    * {
        margin: 0;
        padding: 0;
        font-family: 'Poppins', sans-serif;
        box-sizing: border-box;
    }
    body {
        background: #222;
    }
    .card {
        width: 470px;
        background: linear-gradient(15deg, #00feba, #5b548a);
        color: #fff;
        border-radius: 20px;
        padding: 40px 35px;
        text-align: center;

        .search {
            width: 100%;
            display: flex;
            align-items: center;
            justify-content: space-between;
        }
        .search input {
            border: 0;
            outline: none;
            background: #ebfffc;
            color: #555;
            padding: 10px 25px;
            height: 60px;
            border-radius: 30px;
            flex: 1;
            margin-right: 16px;
            font-size: 18px;
        }
        .search button {
            display: flex;
            align-items: center;
            justify-content: center;
            border: 0;
            outline: none;
            background: #ebfffc;
            border-radius: 50%;
            width: 60px;
            height: 60px;
            cursor: pointer;
        }
        .search button img {
            width: 16px;
        }
        .weather {
            // display: none;
            .weather-icon {
                width: 170px;
            }
            .desc {
                margin-top: -20px;
            }
            h1 {
                font-size: 80px;
                font-weight: 500;
            }
            h2 {
                font-size: 45px;
                font-weight: 400;
                margin-top: -10px;
            }
            .details {
                display: flex;
                align-items: center;
                justify-content: space-between;
                margin-top: 50px;
                .col {
                    display: flex;
                    align-items: center;
                    text-align: left;
                }
                .col img {
                    width: 40px;
                    margin-right: 10px;
                }
                .humidity, .wind {
                    font-size: 28px;
                    margin-top: -6px;
                }
            }
        }
        .error {
            text-align: left;
            margin-left: 10px;
            font-size: 14px;
            margin-top: 10px;
        }
        .slide {
            animation: slide 1s;
        }
        @keyframes slide {
            0% {opacity: 0;}
            100% {opacity: 1;}
        }
    }
    
</style>