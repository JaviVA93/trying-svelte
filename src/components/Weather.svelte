<script lang="ts">
    import { onMount } from "svelte";

    let weatherData: Promise<{
        country: any;
        name: any;
        textCondition: any;
        temp_c: any;
        feelslike_c: any;
    }> = null;

    async function getWeatherData(q: null | string) {
        if (!q) q = "auto:ip";
        q = encodeURI(q);
        const options = {
            method: "GET",
            headers: {
                "X-RapidAPI-Key": import.meta.env.VITE_WEATHERAPI_KEY,
                "X-RapidAPI-Host": "weatherapi-com.p.rapidapi.com",
            },
        };

        try {
            const reqWeatherData = await fetch(
                "https://weatherapi-com.p.rapidapi.com/current.json?q=" + q,
                options
            );
            const resWeatherData = await reqWeatherData.json();

            const { location, current } = resWeatherData,
                { country, name } = location,
                { condition, temp_c, feelslike_c } = current,
                textCondition = condition.text;

            return {
                country,
                name,
                textCondition,
                temp_c,
                feelslike_c,
            };
        } catch (err) {
            console.error(err);
            return null;
        }
    }
    function handleWeatherSubmit(e) {
        const formData = new FormData(e.target);
        const searchLocation = formData.get("search-location");
        weatherData = null;
        setTimeout(() => {
            weatherData = getWeatherData(searchLocation.toString());
        }, 1000);
    }

    onMount(() => {
        setTimeout(() => {
            weatherData = getWeatherData(null);
        }, 1000);
    });
</script>

<main>
    <h1>Weather Component :)</h1>
    <div class="wheatherDataWrapper">
        {#if weatherData}
            {#await weatherData}
                <div class="loadingWeather">
                    <span>Loading weather data...</span>
                    <div class="lds-ripple">
                        <div />
                        <div />
                    </div>
                </div>
            {:then weatherData}
                {#if weatherData === null}
                    <span>Error loading weather data...</span>
                {:else}
                    <h3>Location: {weatherData.name}, {weatherData.country}</h3>
                    <h3>
                        Current weather condition: {weatherData.textCondition}
                    </h3>
                    <span
                        >Temperature: {weatherData.temp_c}ºC, (Feels like: {weatherData.feelslike_c}ºC)</span
                    >
                {/if}
            {/await}
        {:else}
            <div class="loadingWeather">
                <span>Loading weather data...</span>
                <div class="lds-ripple">
                    <div />
                    <div />
                </div>
            </div>
        {/if}
    </div>
    <div class="weatherSearchWrapper">
        <form on:submit|preventDefault={handleWeatherSubmit}>
            <span>The weather in...</span>
            <br />
            <input
                type="text"
                id="search-location"
                name="search-location"
                required
                placeholder="Enter location"
            />
            <button type="submit">Search</button>
        </form>
    </div>
</main>

<style>
    main {
        background-color: rgb(30, 51, 36);
        color: rgb(219, 219, 219);
        padding: 20px;
        border-radius: 8px;
        width: fit-content;
    }
    main h1 {
        margin-bottom: 30px;
    }
    .wheatherDataWrapper {
        display: flex;
        flex-direction: column;
        align-items: flex-start;
        gap: 20px;
    }
    .weatherSearchWrapper {
        width: fit-content;
        margin-top: 20px;
        padding: 15px;
        border-radius: 4px;
        background-color: rgb(71, 70, 70);
    }
    .loadingWeather {
        display: flex;
        flex-direction: column;
        align-items: center;
        gap: 20px;
    }
    .lds-ripple {
        align-self: center;
        display: inline-block;
        position: relative;
        width: 80px;
        height: 80px;
    }
    .lds-ripple div {
        position: absolute;
        border: 4px solid #fff;
        opacity: 1;
        border-radius: 50%;
        animation: lds-ripple 1s cubic-bezier(0, 0.2, 0.8, 1) infinite;
    }
    .lds-ripple div:nth-child(2) {
        animation-delay: -0.5s;
    }
    @keyframes lds-ripple {
        0% {
            top: 36px;
            left: 36px;
            width: 0;
            height: 0;
            opacity: 0;
        }
        4.9% {
            top: 36px;
            left: 36px;
            width: 0;
            height: 0;
            opacity: 0;
        }
        5% {
            top: 36px;
            left: 36px;
            width: 0;
            height: 0;
            opacity: 1;
        }
        100% {
            top: 0px;
            left: 0px;
            width: 72px;
            height: 72px;
            opacity: 0;
        }
    }
</style>
