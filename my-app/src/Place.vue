<template>
  <div>
    <div style="color: #111111; padding: 17px 17px">
      <div
        style="color: #111111; padding: 17px 17px; background-color: #348975"
      >
        <div
          v-for="placePlacePlaceValue in placeValues"
          :key="placePlacePlaceValue.count"
          style="margin: 17px 0px"
        >
          <div>{{ placePlacePlaceValue.first }}</div>
          <div>{{ placePlacePlaceValue.last }}</div>
        </div>
      </div>

      <div>
        <form style="display: flex" v-on:submit="placeIn">
          <input
            type="text"
            style="
              width: 100%;
              padding: 17px 17px;
              background-color: #111111;
              color: #ffffff;
            "
            v-model="value"
          />
        </form>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "Place",

  data: function () {
    return {
      value: "",
      placeValues: [],
      placeValue: {},
      expiredValues: {},
    };
  },
  methods: {
    placeIn: function (e) {
      e.preventDefault();
      var placePlaces = this.value.split(" ");
      if ("SET" == placePlaces[0]) {
        if (3 != placePlaces.length) {
          this.placeLoad("ERROR");
        } else {
          this.placeSetKey(placePlaces[1], placePlaces[2]);
        }
      } else if ("GET" == placePlaces[0]) {
        if (2 != placePlaces.length) {
          this.placeLoad("ERROR");
        } else {
          if ("string" == typeof this.placeValue[placePlaces[1]]) {
            this.placeGetKey(placePlaces[1]);
          } else {
            this.placeLoad("ERROR: Not a String");
          }
        }
      } else if ("SADD" == placePlaces[0]) {
        if (3 > placePlaces.length) {
          this.placeLoad("ERROR");
        } else {
          let placePlaceArr = [];
          for (let i = 0; i < placePlaces.length - 2; ++i) {
            if (-1 == placePlaceArr.indexOf(placePlaces[i + 2])) {
              placePlaceArr.push(placePlaces[i + 2]);
            }
          }
          if (undefined !== this.placeValue[placePlaces[1]]) {
            if ("string" == typeof this.placeValue[placePlaces[1]]) {
              placePlaceArr.push(this.placeValue[placePlaces[1]]);
            }
          }
          this.placeSADDKey(placePlaces[1], placePlaceArr);
        }
      } else if ("SREM" == placePlaces[0]) {
        if (3 > placePlaces.length) {
          this.placeLoad("ERROR");
        } else {
          let placePlaceArr = [];

          for (let i = 0; i < placePlaces.length - 2; ++i) {
            placePlaceArr.push(placePlaces[i + 2]);
          }
          this.placeSREMKey(placePlaces[1], placePlaceArr);
        }
      } else if ("SMEMBERS" == placePlaces[0]) {
        if (2 != placePlaces.length) {
          this.placeLoad("ERROR");
        } else {
          this.placeSMEMBERSKey(placePlaces[1]);
        }
      } else if ("SINTER" == placePlaces[0]) {
        if (3 > placePlaces.length) {
          this.placeLoad("ERROR");
        } else {
          let placePlaceArr = [];
          for (let i = 0; i < placePlaces.length - 1; ++i) {
            placePlaceArr.push(placePlaces[i + 1]);
          }
          this.placeSINTERKey(placePlaceArr);
        }
      } else if ("KEYS" == placePlaces[0]) {
        this.placeKEYS();
      } else if ("DEL" == placePlaces[0]) {
        if (2 != placePlaces.length) {
          this.placeLoad("ERROR");
        } else {
          this.placeDELKey(placePlaces[1]);
        }
      } else if ("EXPIRE" == placePlaces[0]) {
        if (3 != placePlaces.length) {
          this.placeLoad("ERROR");
        } else {
          this.placeEXPIREKey(placePlaces[1], parseInt(placePlaces[2]));
        }
      } else if ("SAVE" == placePlaces[0]) {
        this.placeSAVEKey();
      } else if ("RESTORE" == placePlaces[0]) {
        this.placeRESTOREKey();
      } else if ("TTL" == placePlaces[0]) {
        if (2 != placePlaces.length) {
          this.placeLoad("ERROR");
        } else {
          if (undefined === this.expiredValues[placePlaces[1]]) {
            this.placeLoad("ERROR: Key is not expired");
          } else {
            this.placeTTLKey(placePlaces[1]);
          }
        }
      } else {
        this.placeLoad("ERROR");
      }

      this.value = "";
    },
    placeSetKey: function (key, place) {
      this.placeValue[key] = place;
      this.placeLoad("OK");
    },
    placeGetKey: function (key) {
      let placePlaceValue = this.placeValue[key];
      this.placeLoad(placePlaceValue);
    },
    placeSADDKey: function (key, place) {
      this.placeValue[key] = place;
      this.placeLoad("OK");
    },
    placeSREMKey: function (key, place) {
      let placePlaceValues = [];
      let placePlaceKeys = "";
      placePlaceValues = placePlaceValues.concat(this.placeValue[key]);
      for (let i = 0; i < place.length; ++i) {
        if (-1 == placePlaceValues.indexOf(place[i])) {
          placePlaceKeys = placePlaceKeys + " " + place[i];
        } else {
          placePlaceValues.splice(placePlaceValues.indexOf(place[i]), 1);
        }
      }

      this.placeValue[key] = placePlaceValues;
      this.placeLoad(placePlaceKeys + " " + "is/are not removed");
    },
    placeSMEMBERSKey: function (key) {
      let placePlaceValues = this.placeValue[key];
      this.placeLoad(placePlaceValues.join(" "));
    },
    placeSINTERKey: function (keys) {
      let placePlaceValues = this.placeValue[keys[0]];
      let placeArr = [];
      let count = 0;
      for (let i = 0; i < placePlaceValues.length; ++i) {
        for (let index = 0; index < keys.length - 1; ++index) {
          let placeArray = [];
          placeArray = this.placeValue[keys[index + 1]];
          for (
            let placeIndex = 0;
            placeIndex < placeArray.length;
            ++placeIndex
          ) {
            if (placePlaceValues[i] == placeArray[placeIndex]) {
              count = count + 1;
            }
          }
        }

        if (count == keys.length - 1) {
          placeArr.push(placePlaceValues[i]);
        }
      }

      this.placeLoad(placeArr.join(" "));
    },
    placeKEYS: function () {
      let placePlaceValues = Object.keys(this.placeValue);

      this.placeLoad(placePlaceValues.join(" "));
    },
    placeDELKey: function (key) {
      delete this.placeValue[key];

      if (undefined !== this.expiredValues[key]) {
        delete this.expiredValues[key];
      }
      this.placeLoad("OK");
    },
    placeEXPIREKey: function (key, seconds) {
      let currentDate = new Date();
      setTimeout(() => {
        this.placeDELKey(key);
      }, seconds * 1000);
      this.expiredValues[key] = currentDate.getSeconds() + seconds;
      this.placeLoad(seconds.toString());
    },
    placeSAVEKey: function () {
      let placePlaceValue = this.placeValue;

      window.sessionStorage.setItem("place", JSON.stringify(placePlaceValue));
      this.placeLoad("OK");
    },
    placeRESTOREKey: function () {
      let placePlaceValue = {};
      placePlaceValue = JSON.parse(window.sessionStorage.getItem("place"));
      this.placeValue = placePlaceValue;
      this.placeLoad("OK");
    },
    placeTTLKey: function (key) {
      let currentDate = new Date();

      let placeSeconds = this.expiredValues[key] - currentDate.getSeconds();
      this.placeLoad(placeSeconds + " " + "seconds");
    },
    placeLoad: function (place) {
      let places = {};

      places.last = place;
      places.first = this.value;
      places.count = this.placeValues.length + 1;
      this.placeValues.push(places);
    },
  },
};
</script>
