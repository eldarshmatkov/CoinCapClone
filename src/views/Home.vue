<template>
    <div class="home flex flex-col pb-10 w-screen">
        <header class="pt-10 pb-32 pl-2 pr-2">
            <div class="flex-row wrapper hidden sm:flex">
                <div class="w-1/6 flex flex-col text-white">
                    MARKET CAP
                    <span>$</span>
                </div>
                <div class="w-1/6 flex flex-col text-white">
                    EXCHANGE VOL
                    <span>$</span>
                </div>
                <div class="w-1/6 flex flex-col text-white">
                    ASSETS
                    <span>$</span>
                </div>
                <div class="w-1/6 flex flex-col text-white">
                    EXCHANGES
                    <span>$</span>
                </div>
                <div class="w-1/6 flex flex-col text-white">
                    MARKETS
                    <span>$</span>
                </div>
                <div class="w-1/6 flex flex-col text-white">
                    BTC DOM INDEX
                    <span>$</span>
                </div>
            </div>
        </header>
        <TableContent msg="Welcome to Your Vue.js App"/>
    </div>
</template>

<script>
    // @ is an alias to /src
    import TableContent from "@/components/TableContent.vue";

    export default {
        name: "home",
        components: {
            TableContent
        }
    };
</script>

<style scoped lang="scss">
    header {
        background: linear-gradient(to right, rgb(63, 81, 181), rgb(100, 181, 246)) rgb(255, 255, 255);
    }
</style>
