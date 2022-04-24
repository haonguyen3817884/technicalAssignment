<template>
  <div>
    <div style="color: #111111; padding: 17px 17px">
      <div
        style="color: #111111; padding: 17px 17px; background-color: #348975"
      >
        <div
          v-for="textValue in textValues"
          :key="textValue.count"
          style="margin: 17px 0px"
        >
          <div>{{ textValue.first }}</div>
          <div>{{ textValue.last }}</div>
        </div>
      </div>

      <div>
        <form style="display: flex" v-on:submit="loadInputs">
          <input
            type="text"
            style="
              width: 100%;
              padding: 17px 17px;
              background-color: #111111;
              color: #ffffff;
            "
            v-model="customerInput"
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
      customerInput: "",
      textValues: [],
      storedValue: {},
      expiredValue: {},
    };
  },
  methods: {
    loadInputs: function (e) {
      e.preventDefault();
      var customerInputArr = this.customerInput.split(" ");

      switch (customerInputArr[0]) {
        case "SET":
          if (3 != customerInputArr.length) {
            this.loadTextValues("ERROR");
          } else {
            this.placeSetKey(customerInputArr[1], customerInputArr[2]);
          }
          break;
        case "GET":
          if (2 != customerInputArr.length) {
            this.loadTextValues("ERROR");
          } else {
            if ("string" == typeof this.storedValue[customerInputArr[1]]) {
              this.placeGetKey(customerInputArr[1]);
            } else {
              this.loadTextValues("ERROR: Not a String");
            }
          }
          break;
        case "SADD":
          if (3 > customerInputArr.length) {
            this.loadTextValues("ERROR");
          } else {
            let itemArr = [];
            for (let i = 0; i < customerInputArr.length - 2; ++i) {
              if (-1 == itemArr.indexOf(customerInputArr[i + 2])) {
                itemArr.push(customerInputArr[i + 2]);
              }
            }
            if (undefined !== this.storedValue[customerInputArr[1]]) {
              if ("string" == typeof this.storedValue[customerInputArr[1]]) {
                itemArr.push(this.storedValue[customerInputArr[1]]);
              }
            }
            this.placeSADDKey(customerInputArr[1], itemArr);
          }
          break;
        case "SREM":
          if (3 > customerInputArr.length) {
            this.loadTextValues("ERROR");
          } else {
            let itemArr = [];

            for (let i = 0; i < customerInputArr.length - 2; ++i) {
              itemArr.push(customerInputArr[i + 2]);
            }
            this.placeSREMKey(customerInputArr[1], itemArr);
          }
          break;
        case "SMEMBERS":
          if (2 != customerInputArr.length) {
            this.loadTextValues("ERROR");
          } else {
            this.placeSMEMBERSKey(customerInputArr[1]);
          }
          break;
        case "SINTER":
          if (3 > customerInputArr.length) {
            this.loadTextValues("ERROR");
          } else {
            let itemArr = [];
            for (let i = 0; i < customerInputArr.length - 1; ++i) {
              itemArr.push(customerInputArr[i + 1]);
            }
            this.placeSINTERKey(itemArr);
          }
          break;
        case "KEYS":
          this.placeKEYS();
          break;
        case "DEL":
          if (2 != customerInputArr.length) {
            this.loadTextValues("ERROR");
          } else {
            this.placeDELKey(customerInputArr[1]);
          }
          break;
        case "EXPIRE":
          if (3 != customerInputArr.length) {
            this.loadTextValues("ERROR");
          } else {
            this.placeEXPIREKey(
              customerInputArr[1],
              parseInt(customerInputArr[2])
            );
          }
          break;
        case "SAVE":
          this.placeSAVEKey();
          break;
        case "RESTORE":
          this.placeRESTOREKey();
          break;
        case "TTL":
          if (2 != customerInputArr.length) {
            this.loadTextValues("ERROR");
          } else {
            if (undefined === this.expiredValue[customerInputArr[1]]) {
              this.placeLoad("ERROR: Key is not expired");
            } else {
              this.placeTTLKey(customerInputArr[1]);
            }
          }
          break;
        default:
          this.loadTextValues("ERROR");
          break;
      }

      this.customerInput = "";
    },
    placeSetKey: function (key, itemValue) {
      this.storedValue[key] = itemValue;
      this.loadTextValues("OK");
    },
    placeGetKey: function (key) {
      let item = this.storedValue[key];
      this.loadTextValues(item);
    },
    placeSADDKey: function (key, itemValues) {
      this.storedValue[key] = itemValues;
      this.loadTextValues("OK");
    },
    placeSREMKey: function (key, itemValues) {
      let itemArr = [];
      let leftItems = "";
      itemArr = itemArr.concat(this.storedValue[key]);
      for (let i = 0; i < itemValues.length; ++i) {
        if (-1 == itemArr.indexOf(itemValues[i])) {
          leftItems = leftItems + " " + itemValues[i];
        } else {
          itemArr.splice(itemArr.indexOf(itemValues[i]), 1);
        }
      }

      this.storedValue[key] = itemArr;
      this.loadTextValues(leftItems + " " + "is/are not removed");
    },
    placeSMEMBERSKey: function (key) {
      let itemArr = this.storedValue[key];
      this.loadTextValues(itemArr.join(" "));
    },
    placeSINTERKey: function (keys) {
      let itemArr = this.storedValue[keys[0]];
      let foundItemArr = [];
      let count = 0;
      for (let i = 0; i < itemArr.length; ++i) {
        for (let index = 0; index < keys.length - 1; ++index) {
          let lastItemArr = [];
          lastItemArr = this.storedValue[keys[index + 1]];
          for (
            let arrayIndex = 0;
            arrayIndex < lastItemArr.length;
            ++arrayIndex
          ) {
            if (itemArr[i] == lastItemArr[arrayIndex]) {
              count = count + 1;
            }
          }
        }

        if (count == keys.length - 1) {
          foundItemArr.push(itemArr[i]);
        }
      }

      this.loadTextValues(foundItemArr.join(" "));
    },
    placeKEYS: function () {
      let itemArr = Object.keys(this.storedValue);

      this.loadTextValues(itemArr.join(" "));
    },
    placeDELKey: function (key) {
      delete this.storedValue[key];

      if (undefined !== this.expiredValue[key]) {
        delete this.expiredValue[key];
      }
      this.loadTextValues("OK");
    },
    placeEXPIREKey: function (key, seconds) {
      let currentDate = new Date();
      setTimeout(() => {
        this.placeDELKey(key);
      }, seconds * 1000);
      this.expiredValue[key] = currentDate.getSeconds() + seconds;
      this.loadTextValues(seconds.toString());
    },
    placeSAVEKey: function () {
      let storedValue = this.storedValue;

      window.sessionStorage.setItem("place", JSON.stringify(storedValue));
      this.loadTextValues("OK");
    },
    placeRESTOREKey: function () {
      let storedValue = {};
      storedValue = JSON.parse(window.sessionStorage.getItem("place"));
      this.storedValue = storedValue;
      this.loadTextValues("OK");
    },
    placeTTLKey: function (key) {
      let currentDate = new Date();

      let lastSeconds = this.expiredValue[key] - currentDate.getSeconds();
      this.loadTextValues(lastSeconds + " " + "seconds");
    },
    loadTextValues: function (text) {
      let textValue = {};

      textValue.last = text;
      textValue.first = this.customerInput;
      textValue.count = this.textValues.length + 1;
      this.textValues.push(textValue);
    },
  },
};
</script>
