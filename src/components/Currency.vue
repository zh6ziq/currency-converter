<template>
<div class="container px-0">

    <div class="myheader p-0">
        <h1>Currency Converter</h1>
    </div>

    <div class="row p-3">

        <div class="col-sm-6">
            <label for="actual" class="float-left">From</label>
            <select name="" id="" v-model="from" class="form-control">
                <option :value="currencies.id" v-for="currencies in formatCurrencies" :key="currencies.currencyName">
                    {{ currencies.currencyName }}
                </option>
            </select>
        </div>

        <div class="col-sm-6">
            <label for="final" class="float-left">To</label>
            <select name="" id="" v-model="to" class="form-control">
                <option :value="currencies.id" v-for="currencies in formatCurrencies" :key="currencies.currencyName">
                    {{ currencies.currencyName }}
                </option>
            </select>
        </div>

    </div>

    <div class="row p-3">
        <div class="col-md-6 offset-md-3">
            <input v-model="value" type="text" class="form-control my-5" placeholder="Enter amount">
        </div>
    </div>

    <div class="row">
        <div class="col-md-12 text-center">
            <b-button pill variant="info" @click="convertCurrency()">Convert</b-button>
        </div>
    </div>

    <div class="row mt-5">
        <div class="col-md-2 offset-md-5 text-center">
            <label for="final">Exchange Rate as per {{ timestamp }}</label>
            <h1>{{ calculateResults }}</h1>
        </div>
    </div>

</div>
</template>

<script>
//import axios
import axios from 'axios'

export default {
    data: () => ({
        currencies: {},
        value: null,
        from: 'MYR',
        to: 'USD',
        result: null,
        timestamp: ''
    }),

    mounted() {
        this.getCurrencies();
        setInterval(this.getNow, 1000);

    },

    computed: {
        formatCurrencies() {
            return Object.values(this.currencies)
        },
        calculateResults() {
            return (Number(this.value) * this.result).toFixed(2)
        },

    },

    methods: {
        getCurrencies() {
            const currencies = localStorage.getItem("currencies");

            if (currencies) {
                this.currencies = JSON.parse(currencies)
                return;
            }

            //get real time exchange rate
            axios.get('https://free.currconv.com/api/v7/currencies?apiKey=7c3852999c6529f16732')
                .then(response => {
                    this.currencies = response.data.results;
                    localStorage.setItem('currencies', JSON.stringify(response.data.results))
                })
                .catch(err => {
                    console.log(err)
                    alert('System error occured')
                })
        },

        //convert currency using API
        convertCurrency() {
            const search = `${this.from}_${this.to}`
            axios.get(`https://free.currconv.com/api/v7/convert?q=${search}&apiKey=7c3852999c6529f16732`)
                .then((response) => {
                    // console.log(response)
                    this.result = response.data.results[search].val;
                })
                .catch(err => {
                    console.log(err)
                    alert('System error occured')
                })
        },

        //current date & time
        getNow: function () {
            const today = new Date();
            const date = today.getDate() + '/' + (today.getMonth() + 1) + '/' + today.getFullYear();
            const time = today.getHours() + ":" + today.getMinutes() + ":" + today.getSeconds();
            const dateTime = date + ' ' + time;
            this.timestamp = dateTime;
        }

    },

    //reset amount to 0 when currency changed
    watch: {
        from() {
            this.result = 0
        },
        to() {
            this.result = 0
        }
    }

};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->

<style>
.container {
    background: rgb(253, 251, 230);
    margin: 60px auto;
    border: 10px solid black;

}

.myheader {
    background: rgb(0, 108, 134);
    color: white;
}

h1 {
    border-bottom: 2px solid black;
}

h3 {
    margin: 40px 0 0;
}

ul {
    list-style-type: none;
    padding: 0;
}

li {
    display: inline-block;
    margin: 0 10px;
}

a {
    color: #42b983;
}
</style>
