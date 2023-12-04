<!-- 2023年12月4日23:58:06 -->
<script setup >
import { ref } from 'vue';
// 普通手机号
const cellphoneNumber = ref([
    "10890668000",
    "14679718290",
    "16762595537",
    "18563116663",
    "16176700489",
    "17394355969",
    "16783752760",
    "18084260551",
    "12598364605",
    "13756145763",
    "14348536097",
    "15760862338",
    "18353786754",
    "18265742102",
    "15131529300",
    "12209335366",
    "11573476932",
    "14750771752",
    "19353706380",
    "17428007697"
])
// 普通兑换码
const exchangeCode = ref([
    'cdekfgh',
    'klmknop',
    'stukvwx',
    'zabkcde',
    'hijkklm',
    'pqrkstu',
    'xyzkabc',
    'fghkijk',
    'nopkrst',
    'xyzkabc',
    'jklkmno',
    'tuvkwxy',
    'fghkijk',
    'lmnkopq',
    'uvaklbc',
    'ghikjkl',
    'pqrkstu',
    'bcdkefg',
    'klmknop',
    'klmknoq',
])
// 白名单手机号
const whiteListhoneNumber = ref([
    "10890668000",
    "14679718290",
    "16762595537",
    "18563116663",
    "16176700489",
    "17394355969",
    "16783752760",
    "18084260551",
    "12598364605",
    "13756145763",
    "14348536097",
    "15760862338",
    "18353786754",
    "18265742102",
    "15131529300",
    "12209335366",
    "11573476932",
    "14750771752",
    "19353706380",
    "17428007697"
])
// 白名单兑换码
const whiteListexchange = ref([
    'cdekfgh',
    'klmknop',
    'stukvwx',
    'zabkcde',
    'hijkklm',
    'pqrkstu',
    'xyzkabc',
    'fghkijk',
    'nopkrst',
    'xyzkabc',
    'jklkmno',
    'tuvkwxy',
    'fghkijk',
    'lmnkopq',
    'uvaklbc',
    'ghikjkl',
    'pqrkstu',
    'bcdkefg',
    'klmknop',
])
const buttonText = ref('开始抽奖');
const selectedPhoneNumber = ref('');
const selectedExchangeCode = ref('');
let intervalId = null;
const isLotteryRunning = ref(false);
const hiddenPhoneNumber = ref('****');
const hiddenExchangeCode = ref('***');


// 隐藏兑换码手机号
function updateHiddenValues() {
    if (selectedPhoneNumber.value) {
        const visibleDigits = selectedPhoneNumber.value.slice(0, 3) + '****' + selectedPhoneNumber.value.slice(-4);
        hiddenPhoneNumber.value = visibleDigits;
    }
    if (selectedExchangeCode.value) {
        const visibleCode = selectedExchangeCode.value.slice(0, 2) + '***' + selectedExchangeCode.value.slice(-2);
        hiddenExchangeCode.value = visibleCode;
    }
}

// 开始抽奖或停止
function toggleLottery() {
    if (isLotteryRunning.value) {
        stopLottery();
    } else {
        startLottery();
    }
}

// 开始抽奖
function startLottery() {
    isLotteryRunning.value = true;
    buttonText.value = '暂停抽奖';
    intervalId = setInterval(() => {
        const randomIndex = Math.floor(Math.random() * cellphoneNumber.value.length);
        selectedPhoneNumber.value = cellphoneNumber.value[randomIndex];
        selectedExchangeCode.value = exchangeCode.value[randomIndex];
        updateHiddenValues();
    }, 100);
}

// 停止抽奖
function stopLottery() {
    isLotteryRunning.value = false;
    buttonText.value = '开始抽奖';
    clearInterval(intervalId);
    const randomIndex = Math.floor(Math.random() * cellphoneNumber.value.length);
    selectedPhoneNumber.value = cellphoneNumber.value[randomIndex];
    selectedExchangeCode.value = exchangeCode.value[randomIndex];
    updateHiddenValues();
}
</script>

<template>
    <div class="raffleBoxStyle">
        <div class="raffleBox">
            <div class="box1">
                <h1>
                    <h1>{{ isLotteryRunning ? hiddenExchangeCode : hiddenExchangeCode }}</h1>
                </h1>
            </div>
            <div class="box2">
                <h1>
                    <h1>{{ isLotteryRunning ? hiddenPhoneNumber : hiddenPhoneNumber }}</h1>
                </h1>
            </div>
        </div>
        <div class="lottery">
            <button @click="toggleLottery">{{ buttonText }}</button>
        </div>
    </div>
</template>

<style lang="less" scoped>
.raffleBoxStyle {
    width: 100%;
    height: 100%;
    display: flex;
    flex-direction: column;
    justify-content: space-evenly;
    align-items: center;

    .raffleBox {
        width: 750px;
        height: 100px;
        display: flex;
        justify-content: space-evenly;
        align-items: center;

        .box1 {
            border: 1px solid #000;
            width: 300px;
            height: 100px;
            display: flex;
            justify-content: space-evenly;
            align-items: center;
            font-size: 30px;
        }

        .box2 {
            border: 1px solid #000;
            width: 300px;
            height: 100px;
            display: flex;
            justify-content: space-evenly;
            align-items: center;
            font-size: 30px;
        }


    }

    .lottery {
        width: 100px;
        height: 30px;

        button {
            width: 150px;
            height: 50px;
            font-size: 20px;
        }

    }
}
</style>
