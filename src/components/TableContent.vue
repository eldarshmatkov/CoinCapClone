<template>
    <div class="table-content-wrapper self-center -mt-40 sm:-mt-20 text-sm sm:text-base  rounded-lg shadow-lg overflow-hidden w-11/12">
        <div class="flex flex-row bg-gray-200 p-2 border-b border-gray-400 cursor-pointer">
            <div class="flex flex-1 justify-center items-center table-title">Rank</div>
            <div class="flex flex-1 sm:flex-3 justify-center items-center table-title">Name</div>
            <div class="flex flex-1 justify-center items-center table-title">Price</div>
            <div class="hidden lg:flex flex-1 justify-center items-center table-title">Market Cap</div>
            <div class="hidden lg:flex flex-1 justify-center items-center table-title">VWAP (24Hr)</div>
            <div class="hidden lg:flex flex-1 justify-center items-center table-title">Supply</div>
            <div class="hidden lg:flex flex-1 justify-center items-center table-title">Volume (24Hr)</div>
            <div class="hidden lg:flex flex-1 justify-center items-center table-title">Change (24Hr)</div>
        </div>
        <div class="table-content" v-if="currency.data && currency">
            <div class="flex flex-row bg-white p-2 border-b border-gray-400 cursor-pointer"
                 v-for="(item, index) of currency.data.slice(0, 15)" :key="index">
                <div class="flex flex-1 justify-center">{{item.rank}}</div>
                <div class="flex flex-1 sm:flex-3 justify-center">{{item.name}}</div>
                <div class="flex flex-1 justify-center">{{item.priceUsd | currency}}</div>
                <div class="hidden lg:flex flex-1 justify-center">{{item.marketCapUsd | convertNumbers}}</div>
                <div class="hidden lg:flex flex-1 justify-center">{{item.vwap24Hr | currency}}</div>
                <div class="hidden lg:flex flex-1 justify-center">{{item.supply | convertNumbers}}</div>
                <div class="hidden lg:flex flex-1 justify-center">{{item.volumeUsd24Hr | convertNumbers}}</div>
                <div class="hidden lg:flex flex-1 justify-center" :class="checkIncome(item.changePercent24Hr)">{{
                    Number(item.changePercent24Hr).toFixed(2) + '%'}}
                </div>
            </div>
        </div>
    </div>
</template>

<script>
    import axios from 'axios';

    export default {
        data: () => ({
            currency: [],
            errors: [],
            ws: null
        }),
        methods: {
            checkIncome: function (number) {
                let checkNumberAffinity = Math.sign(Number(number));
                if (checkNumberAffinity > 0) {
                    return 'text-green-600'
                } else if (checkNumberAffinity < 0) {
                    return 'text-red-600'
                }
            }
        },
        created() {
            axios.get('https://api.coincap.io/v2/assets')
                .then(response => {
                    this.currency = response.data
                })
                .catch(e => {
                    this.errors.push(e);
                });
            let self = this;

            this.ws = new WebSocket('wss://ws.coincap.io/prices?assets=bitcoin,ethereum,monero,litecoin');

            this.ws.onmessage = function (msg) {
                let parsedJson = JSON.parse(msg.data);
                Object.keys(parsedJson)
                    .forEach(function eachKey(key) {
                        let getSimilarItemIndex = self.currency.data.findIndex(x => x.id === key);
                        self.currency.data[getSimilarItemIndex].priceUsd = parsedJson[key]
                    });
            };
        },
        filters: {
            convertNumbers: function (labelValue) {
                // Nine Zeroes for Billions
                return Math.abs(Number(labelValue)) >= 1.0e+9

                    ? '$ ' + (Math.abs(Number(labelValue)) / 1.0e+9).toFixed(2) + "B"
                    // Six Zeroes for Millions
                    : Math.abs(Number(labelValue)) >= 1.0e+6

                        ? '$ ' + (Math.abs(Number(labelValue)) / 1.0e+6).toFixed(2) + "M"
                        // Three Zeroes for Thousands
                        : Math.abs(Number(labelValue)) >= 1.0e+3

                            ? '$ ' + (Math.abs(Number(labelValue)) / 1.0e+3).toFixed(2) + "K"

                            : Math.abs(Number(labelValue));

            }
        }
    };
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss">
    .table-content {
        height: calc(100vh - 5rem - 163px);
        max-height: 639px;
        overflow: scroll;

        .table-title {
            &::after {
                content: '';
            }
        }
    }

    @media (max-width: 1024px) {
        .table-content {
            height: calc(100vh - 61px);
            max-height: 570px;
        }
    }
</style>
