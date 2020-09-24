<template>
  <div id="app">
    <div>Euro foreign exchange reference rates: <span>{{dateUpdated}}</span></div>
    <div>Base currency: <span>{{baseCurrency}}</span></div>
    <div>
      Count <input v-model="count" type="text">
      From: <select v-model="from">
        <option
            v-for="currency in currencies"
            :value="currency.code"
            :key="currency.code"
        >{{currency.name}} ({{currency.code}})</option>
      </select>
      To: <select v-model="to">
        <option
            v-for="currency in currencies"
            :value="currency.code"
            :key="currency.code"
        >{{currency.name}} ({{currency.code}})</option>
      </select>
      <input type="button" value="Convert" @click="convert()">
    </div>
    <div>
      Result:  <span>{{result}}</span>
    </div>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  name: 'App',
  data () {
    return {
      count: 1,
      from: "EUR",
      to: "USD",
      result: "",
      currencies: [],
      dateUpdated: "",
      baseCurrency: ""
    }
  },
  components: {},
  mounted() {
    axios
        .get('http://127.0.0.1:8000/currency/')
        .then( response => {
          this.currencies = response.data;

          this.currencies.forEach((currency) => {
            if (currency.is_base) {
              this.baseCurrency = `${currency.name} (${currency.code})`;
            }
          });
        });

    axios
        .get('http://127.0.0.1:8000/date/updated/')
        .then( response => {
          console.log(response.data.date);
          this.dateUpdated = response.data.date;
        });
  },
  methods: {
    convert: function () {
      let count = parseFloat(this.count.toString().replace(",", "."));
      axios
          .get(`http://127.0.0.1:8000/exchange/${this.from}/${this.to}/${count}/`)
          .then(response => {
            this.result = `${response.data.result} ${this.to}`;
          });
    }
  }
}
</script>

<style>
  input[type=text] {
    width: 100px;
  }

  input[type=button] {
    margin-left: 10px;
  }

  #app div {
    padding: 5px;
  }
</style>
