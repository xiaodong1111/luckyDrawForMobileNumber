<!-- 2023年12月5日20:15:18 -->
<script setup >
import { onMounted, ref } from 'vue';
// 普通手机号
const cellphoneNumber = ref([
])
// 普通兑换码
const exchangeCode = ref([

])
// 白名单手机号
const whiteListhoneNumber = ref([

])
// 白名单兑换码
const whiteListexchange = ref([

])
const buttonText = ref('开始抽奖');
const selectedPhoneNumber = ref('');
const selectedExchangeCode = ref('');
let intervalId = null;
const isLotteryRunning = ref(false);
const hiddenPhoneNumber = ref('****');
const hiddenExchangeCode = ref('***');
const winners = ref([]); // 存储抽奖获胜者的数组

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

    // 筛选普通号码列表，排除已中奖号码
    const filteredNumbers = cellphoneNumber.value.filter(
        number => !winners.value.some(winner => winner.phoneNumber === number)
    );

    // 更新普通号码列表
    cellphoneNumber.value = filteredNumbers;

    // 筛选白名单号码列表，排除已中奖号码
    const filteredWhiteListNumbers = whiteListhoneNumber.value.filter(
        number => !winners.value.some(winner => winner.phoneNumber === number)
    );

    // 更新白名单号码列表
    whiteListhoneNumber.value = filteredWhiteListNumbers;

    intervalId = setInterval(() => {
        let randomIndex;
        if (Math.random() < 0.5) {
            // 从白名单中随机选择
            randomIndex = Math.floor(Math.random() * whiteListhoneNumber.value.length);
            selectedPhoneNumber.value = whiteListhoneNumber.value[randomIndex];
            selectedExchangeCode.value = whiteListexchange.value[randomIndex];
        } else {
            // 如果不在白名单中，则从普通列表中随机选择
            randomIndex = Math.floor(Math.random() * cellphoneNumber.value.length);
            selectedPhoneNumber.value = cellphoneNumber.value[randomIndex];
            selectedExchangeCode.value = exchangeCode.value[randomIndex];
        }
        updateHiddenValues();
    }, 100);
}

// 停止抽奖
function stopLottery() {
    isLotteryRunning.value = false;
    buttonText.value = '开始抽奖';
    clearInterval(intervalId);

    const winner = {
        phoneNumber: selectedPhoneNumber.value,
        exchangeCode: selectedExchangeCode.value
    };

    winners.value.push(winner); // 将获胜者添加到数组中

    // 更新隐藏值
    updateHiddenValues();

    // 存储到本地
    saveWinnersToLocalStorage();

    // 如果白名单手机号已抽完，则切换到普通抽奖模式
    if (whiteListhoneNumber.length === winners.value.length) {
        switchToRegularMode();
    }
}
// 保存获胜者信息到本地存储
function saveWinnersToLocalStorage() {
    localStorage.setItem('winners', JSON.stringify(winners.value));
}
// 切换到普通抽奖模式
function switchToRegularMode() {
    cellphoneNumber.value = cellphoneNumber.value.filter(
        number => !winners.value.some(winner => winner.phoneNumber === number)
    );

    exchangeCode.value = exchangeCode.value.filter(
        code => !winners.value.some(winner => winner.exchangeCode === code)
    );

    whiteListhoneNumber.value = [];
    whiteListexchange.value = [];

    // 重新开始抽奖
    startLottery();
}
onMounted(() => {
    fetch("/cellphoneNumber.json")
        .then((response) => response.json())
        .then((data) => {
            cellphoneNumber.value = data;
        })
        .catch((error) => {
            console.error("Error:", error);
        });
    fetch("/exchangeCode.json")
        .then((response) => response.json())
        .then((data) => {
            exchangeCode.value = data;
        })
        .catch((error) => {
            console.error("Error:", error);
        });
    fetch("/whiteListhoneNumber.json")
        .then((response) => response.json())
        .then((data) => {
            whiteListhoneNumber.value = data
        })
        .catch((error) => {
            console.error("Error:", error);
        });
    fetch("/whiteListexchange.json")
        .then((response) => response.json())
        .then((data) => {
            whiteListexchange.value = data
        })
        .catch((error) => {
            console.error("Error:", error);
        });
})
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
