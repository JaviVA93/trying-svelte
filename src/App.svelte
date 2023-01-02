<script lang="ts">
  async function getWeatherData(q: null | string) {
    if (!q)
      q = 'auto:ip';
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
    const searchLocation = formData.get('search-location');
    weatherData = getWeatherData(searchLocation.toString());
  }

  let weatherData = getWeatherData(null);
</script>

<main>
  <h1>Weather Page :)</h1>
  <div class="wheatherDataWrapper">
    {#await weatherData}
      <span>Loading weather data...</span>
    {:then weatherData}
      {#if weatherData === null}
        <span>Error loading weather data...</span>
      {:else}
        <h3>Location: {weatherData.name}, {weatherData.country}</h3>
        <h3>Current weather condition: {weatherData.textCondition}</h3>
        <span>Temperature: {weatherData.temp_c}ºC, (Feels like: {weatherData.feelslike_c}ºC)</span>
      {/if}
    {/await}
  </div>
  <div class="weatherSearchWrapper">
    <form on:submit|preventDefault={handleWeatherSubmit}>
      <span>The weather in...</span>
      <br>
      <input type="text" id="search-location" name="search-location" required placeholder="Enter location"/>
      <button type="submit">Search</button>
    </form>
  </div>
</main>

<style>
  main {
    font-family: system-ui, -apple-system, BlinkMacSystemFont, "Segoe UI",
      Roboto, Oxygen, Ubuntu, Cantarell, "Open Sans", "Helvetica Neue",
      sans-serif;
    width: 100vw;
    height: 100vh;
    background-color: rgb(41, 40, 40);
    color: rgb(219, 219, 219);
    padding: 20px;
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
    margin: 20px 0;
    padding: 15px;
    border-radius: 4px;
    background-color: rgb(71, 70, 70);
  }
</style>
