<template>
  <q-page style="" id="mainPage" class="mainPage">
    <div id="wrapper" class="wrapper">
      <div class="userChoice" v-show="choice == 0">
        <div class="textBox">
          <h4 v-if="city == ''">Введіть назву міста</h4>
          <h4 v-else>Погода в місті {{ city }}</h4>
        </div>
        <div class="inputBox">
          <input
            type="text"
            v-model="city"
            placeholder="Назва міста"
            name="CityName"
            id="City"
            class="request"
          />
          <input
            type="button"
            @click="GetCity()"
            id="submitButton"
            value="Відправити"
            class="request"
          />
        </div>
      </div>
      <div class="userChoice" v-show="choice == 1">
        <div class="textBox">
          <h4 id="coordText" v-if="city == ''">Введіть координати міста</h4>
          <h4 v-else>Погода в місті {{ city }}</h4>
        </div>
        <div class="inputBox">
          <input
            type="text"
            v-model="lon"
            placeholder="Довгота"
            name="Lon"
            id="Coords"
            class="request"
          />
          <input
            type="text"
            v-model="lat"
            placeholder="Широта"
            name="Lat"
            id="Coords"
            class="request"
          />
          <input
            type="button"
            @click="GetCity()"
            id="submitButton"
            value="Відправити"
            class="request"
          />
        </div>
      </div>
      <div class="data">
        <div class="Param">
          <h5 style="margin-right: 5px">Температура:</h5>
          <h5 id="Temp"></h5>
        </div>

        <div class="Param">
          <h5 style="margin-right: 5px">Відчувається як:</h5>
          <h5 id="TempFeels"></h5>
        </div>

        <div class="Param">
          <h5 style="margin-right: 5px">Погода:</h5>
          <q-img
            id="WeatherIcon"
            :src="this.weatherIconUrl"
            style="width: 48px; height: 48px"
          />
          <h5 id="Weather"></h5>
        </div>
      </div>
      <div class="choiceBox">
        <div
          @click="
            choice = 0;
            UserChoice();
          "
          id="firstChoice"
          class="choice"
        >
          <h5>За назвою</h5>
        </div>
        <div
          @click="
            choice = 1;
            UserChoice();
          "
          id="secondChoice"
          class="choice"
        >
          <h5>За координатами</h5>
        </div>
      </div>
    </div>
  </q-page>
</template>

<script>
import { defineComponent, ref } from "vue";

export default {
  defineComponent() {
    name: "IndexPage";
  },
  data() {
    return {
      key: "",
      city: "",
      lon: "",
      lat: "",
      choice: 0,
      weatherIconUrl: "",
      weatherIcon: "",
      dnCycle: "d",
      filePath: "src/stuff/code.txt",
    };
  },
  methods: {
    async getKey() {
      try {
        const response = await fetch(this.filePath);
        const data = await response.text();
        return data.toString();
      } catch (error) {
        console.log("Помилка: " + error);
      }
    },
    async GetCity() {
      this.key = await this.getKey();
      let cityName = document.getElementById("City").value;
      let urlLink;
      if (this.choice == 0) {
        urlLink = `https://api.openweathermap.org/data/2.5/weather?q=${cityName}&appid=${this.key.toString()}&units=metric&lang=ua`;
      } else if (this.choice == 1) {
        urlLink = `https://api.openweathermap.org/data/2.5/weather?lat=${this.lat}&lon=${this.lon}&appid=${this.key}&units=metric&lang=ua`;
      }
      fetch(urlLink)
        .then((response) => response.json())
        .then((data) => {
          this.city = data.name;
          this.lat = data.coord.lat;
          this.lon = data.coord.lon;
          const temperature = data.main.temp;
          const temperatureFeels = data.main.feels_like;
          const weather = data.weather[0].description;
          this.weatherIcon = data.weather[0].icon;
          this.weatherIconUrl = `https://openweathermap.org/img/w/${this.weatherIcon}.png`;
          console.log(this.weatherIconUrl);
          document.getElementById("City").innerHTML = this.city;
          document.getElementById("Temp").innerHTML = temperature + "&#8451";
          document.getElementById("TempFeels").innerHTML =
            temperatureFeels + "&#8451";
          document.getElementById("Weather").innerHTML = weather;
          this.GetColors();
          this.UserChoice();
        });
    },
    UserChoice() {
      if (this.choice == 0) {
        if (this.dnCycle == "d") {
          document.getElementById("firstChoice").style.backgroundColor =
            "#887B69";
          document.getElementById("firstChoice").style.color = "azure";

          document.getElementById("secondChoice").style.backgroundColor =
            "antiquewhite";
          document.getElementById("secondChoice").style.color = "black";
        } else if (this.dnCycle == "n") {
          document.getElementById("firstChoice").style.backgroundColor =
            "#3d3144";
          document.getElementById("firstChoice").style.color = "azure";

          document.getElementById("secondChoice").style.backgroundColor =
            "#695475";
          document.getElementById("secondChoice").style.color = "azure";
        }
      } else if (this.choice == 1) {
        if (this.dnCycle == "d") {
          document.getElementById("firstChoice").style.backgroundColor =
            "antiquewhite";
          document.getElementById("firstChoice").style.color = "black";

          document.getElementById("secondChoice").style.backgroundColor =
            "#887B69";
          document.getElementById("secondChoice").style.color = "azure";
        } else if (this.dnCycle == "n") {
          document.getElementById("firstChoice").style.backgroundColor =
            "#695475";
          document.getElementById("firstChoice").style.color = "azure";

          document.getElementById("secondChoice").style.backgroundColor =
            "#3d3144";
          document.getElementById("secondChoice").style.color = "azure";
        }
      }
    },
    GetColors() {
      let iconName = "";
      let weather, nameLength;
      if (this.weatherIconUrl === "" || iconName != "") {
        iconName = "04d";
      } else {
        iconName = this.weatherIcon;
      }
      nameLength = iconName.length;
      weather = iconName.slice(0, nameLength - 1);
      this.dnCycle = iconName.slice(weather.length);
      console.log("Weather " + weather + "\nDay or night " + this.dnCycle);
      if (this.dnCycle == "d") {
        //Top gradient color + toolbar
        document
          .getElementById("mainPage")
          .style.setProperty("--time-color", "#fdbb2d");
        document.getElementById("toolbar").style.background = "#fdbb2d";
        document.getElementById("toolbar").style.color = "black";
        this.ChangeMode(0);
      } else {
        document
          .getElementById("mainPage")
          .style.setProperty("--time-color", "#402750");
        document.getElementById("toolbar").style.background = "#402750";
        document.getElementById("toolbar").style.color = "azure";
        this.ChangeMode(1);
      }
      switch (
        weather //Lower gradient color
      ) {
        case "01": //clear sky
          document
            .getElementById("mainPage")
            .style.setProperty(
              "--weather-color",
              "rgba(255,227,36,0.9920168751094187)"
            );
          break;
        case "02": //few clouds
          document
            .getElementById("mainPage")
            .style.setProperty(
              "--weather-color",
              "rgba(208,176,125,0.9920168751094187)"
            );
          break;
        case "03": //scattered clouds
          document
            .getElementById("mainPage")
            .style.setProperty(
              "--weather-color",
              "rgba(213,207,197,0.9920168751094187)"
            );
          break;
        case "04": //broken clouds
          document
            .getElementById("mainPage")
            .style.setProperty(
              "--weather-color",
              "rgba(108,105,100,0.9920168751094187)"
            );
          break;
        case "09": //shower rain
          document
            .getElementById("mainPage")
            .style.setProperty(
              "--weather-color",
              "rgba(51,103,108,0.9920168751094187)"
            );
          break;
        case "10": //rain
          document
            .getElementById("mainPage")
            .style.setProperty(
              "--weather-color",
              "rgba(85,175,184,0.9920168751094187)"
            );
          break;
        case "11": //thunderstorm
          document
            .getElementById("mainPage")
            .style.setProperty(
              "--weather-color",
              "rgba(15,61,116,0.9920168751094187)"
            );
          break;
        case "13": //snow
          document
            .getElementById("mainPage")
            .style.setProperty(
              "--weather-color",
              "rgba(230,230,230,0.9920168751094187)"
            );
          break;
        case "50": //Mist
          document
            .getElementById("mainPage")
            .style.setProperty(
              "--weather-color",
              "rgba(230,230,230,0.9920168751094187)"
            );
          break;
      }
    },
    ChangeMode(choice) {
      //Day or Night mode
      let Wrapper = document.getElementById("wrapper").style;
      let requests = document.getElementsByClassName("request");
      if (choice == 0) {
        Wrapper.backgroundColor = "burlywood";
        Wrapper.color = "black";
        console.log(requests.length);
        for (let i = 0; i < requests.length; i++) {
          console.log(i + "\n");
          requests[i].style.backgroundColor = "#faebd7";
          requests[i].style.color = "black";
        }
        //coords.backgroundColor = "#faebd7";
        //submBut.backgroundColor = "#faebd7";
        //city.backgroundColor = "#faebd7";
      } else {
        Wrapper.backgroundColor = "#2e1c39";
        Wrapper.color = "azure";
        console.log(requests.length);
        for (let i = 0; i < requests.length; i++) {
          console.log(i + "\n");
          requests[i].style.backgroundColor = "#3d3144";
          requests[i].style.color = "azure";
        }
        //coords.backgroundColor = "#3d3144";
        //submBut.backgroundColor = "#3d3144";
        //city.backgroundColor = "#3d3144";
      }
    },
  },
};
</script>

<style scoped>
@property --weather-color {
  syntax: "<color>";
  inherits: false;
  initial-value: transparent;
}
@property --time-color {
  syntax: "<color>";
  inherits: false;
  initial-value: transparent;
}

@media screen {
  .mainPage {
    --weather-color: rgba(240, 232, 187, 1);
    --time-color: rgba(205, 236, 249, 1);

    display: flex;
    justify-content: center;
    align-items: center;
    background: linear-gradient(
      0deg,
      var(--weather-color) -10%,
      var(--time-color) 73%
    );
    transition: --weather-color, --time-color 500ms;
  }
  .Param {
    display: flex;
    flex-direction: row;
    align-items: center;
    justify-content: flex-start;
    height: 50px;
  }
  .wrapper {
    display: flex;
    flex-direction: column;
    width: 550px;
    min-height: 400px;
    border-radius: 30px;
    border: 2px black solid;
    justify-content: space-around;
    align-items: center;
    background-color: #deb887;
    transition: 500ms ease-in-out;
  }
  .userChoice,
  .choiceBox {
    flex: 1;
  }
  .request {
    background-color: #faebd7;
    border: 2px black solid;
    box-shadow: none;
    border-radius: 8px;
    transition: 500ms ease-in-out;
  }
  .request#submitButton {
    margin-left: 5px;
    min-width: 100px;
    height: 100%;
    padding: 5px;
    font-size: larger;
    border-radius: 0px 8px 0px 0px;
    flex: 2;
    padding: 0px;
  }
  .request#City {
    width: 200px;
    height: 100%;
    outline: none;
    border-radius: 8px 0px 0px 0px;
    font-size: larger;
    flex: 1;
    text-align: center;
  }
  .request#Coords {
    width: 100px;
    height: 100%;
    outline: none;
    border-radius: 8px 0px 0px 0px;
    font-size: larger;
    flex: 1;
    text-align: center;
  }
  .request#Coords:nth-child(2) {
    border-radius: 0px;
    margin-left: 5px;
  }
  p {
    width: 50%;
  }
  .request#submitButton:hover {
    background-color: rgb(136, 123, 105);
    color: azure;
  }
  .data {
    display: flex;
    flex-direction: column;
    flex: 2;
    width: 329px;
    justify-content: space-evenly;
    align-items: space-evenly;
  }
  .data p {
    margin-top: 15px;
  }
  .inputBox {
    display: flex;
    justify-content: space-evenly;
    margin-top: 10px;
    height: 40px;
    transition: 500ms ease-in-out;
  }
  .textBox {
    display: flex;
    justify-content: center;
    margin-top: 10px;
  }
  .choiceBox {
    display: flex;
    flex-direction: row;
    align-items: flex-end;
    justify-content: space-around;
    position: relative;
    padding: 15px;
    width: 100%;
    margin-bottom: -15px;
  }
  .choice {
    display: flex;
    height: 50px;
    align-items: center;
    justify-content: center;
    width: 100%;
    border-top: 1px black solid;
    transition: 500ms ease-in-out;
  }
  .choice:nth-child(1) {
    border-radius: 0px 0px 0px 28px;
    border-right: 2px black solid;
    background-color: rgb(136, 123, 105);
  }
  .choice:nth-child(2) {
    border-radius: 0px 0px 28px 0px;
    background-color: antiquewhite;
  }
  .userChoice {
    max-width: 90%;
    width: 90%;
  }
}

@media screen and (max-width: 576px) {
  .wrapper {
    font-size: small;
    display: flex;
    flex-direction: column;
    width: 300px;
    min-height: 400px;
    border-radius: 30px;
    border: 2px black solid;
    justify-content: space-around;
    align-items: center;
    background-color: burlywood;
  }
  .userChoice,
  .choiceBox {
    flex: 1;
  }
  .userChoice {
    width: 100%;
  }
  .request {
    background-color: antiquewhite;
    border: 2px black solid;
    box-shadow: none;
    border-radius: 8px;
  }
  .request#submitButton {
    width: 100px;
    max-width: 100px;
    height: 100%;
    font-size: larger;
    border-radius: 0px 8px 0px 0px;
    flex: 1;
  }
  .request#City {
    width: 100px;
    max-width: 100px;
    height: 100%;
    outline: none;
    border-radius: 8px 0px 0px 0px;
    font-size: larger;
    flex: 1;
    text-align: center;
  }
  .request#Coords {
    max-width: 70px;
    height: 100%;
    outline: none;
    border-radius: 8px 0px 0px 0px;
    padding: 0px 2px;
    font-size: medium;
    flex: 1;
    text-align: center;
  }
  .request#Coords:nth-child(2) {
    border-radius: 0px;
  }
  .request#submitButton:hover {
    background-color: rgb(136, 123, 105);
    color: azure;
  }
  .data {
    display: flex;
    flex-direction: column;
    flex: 2;
    width: 100%;
    justify-content: space-evenly;
    align-items: space-evenly;
  }
  .data h5 {
    font-size: 18px;
    margin-left: 10px;
  }
  .Param {
    display: flex;
    flex-direction: row;
    align-items: center;
    justify-content: flex-start;
    height: 50px;
  }
  .inputBox {
    display: flex;
    justify-content: space-evenly;
    margin-top: 10px;
  }
  .textBox {
    display: flex;
    justify-content: center;
    margin-top: 10px;
  }
  .textBox h4 {
    font-size: 24px;
  }
  .textBox h4#coordText {
    font-size: 20px;
  }
  .choiceBox {
    display: flex;
    flex-direction: row;
    align-items: flex-end;
    justify-content: space-around;
    position: relative;
    padding: 15px;
    width: 100%;
    margin-bottom: -15px;
  }
  .choice {
    display: flex;
    height: 50px;
    align-items: center;
    justify-content: center;
    width: 100%;
    border-top: 1px black solid;
    transition: 500ms ease-in-out;
  }
  .choice:nth-child(1) {
    border-radius: 0px 0px 0px 28px;
    border-right: 2px black solid;
    background-color: rgb(136, 123, 105);
  }
  .choice:nth-child(2) {
    border-radius: 0px 0px 28px 0px;
    background-color: antiquewhite;
  }
  .choice h5 {
    font-size: 16px;
  }
}
</style>
