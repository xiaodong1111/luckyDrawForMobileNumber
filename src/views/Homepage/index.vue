<!-- 2023年12月6日10:52:12 -->
<script setup >
import { onMounted, ref } from 'vue';
import * as XLSX from 'xlsx';
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
// / 开始抽奖
function startLottery() {
    isLotteryRunning.value = true;
    buttonText.value = '暂停抽奖';

    intervalId = setInterval(() => {
        // 筛选可用的普通号码和兑换码
        let availableNumbers = cellphoneNumber.value.filter(
            number => !winners.value.some(winner => winner.phoneNumber === number)
        );
        let availableCodes = exchangeCode.value.filter(
            code => !winners.value.some(winner => winner.exchangeCode === code)
        );

        // 筛选可用的白名单号码和兑换码
        const availableWhiteListNumbers = whiteListhoneNumber.value.filter(
            number => !winners.value.some(winner => winner.phoneNumber === number)
        );
        const availableWhiteListCodes = whiteListexchange.value.filter(
            code => !winners.value.some(winner => winner.exchangeCode === code)
        );

        let randomIndex;
        if (Math.random() < 0.5 && availableWhiteListNumbers.length > 0 && availableWhiteListCodes.length > 0) {
            // 从白名单中随机选择
            randomIndex = Math.floor(Math.random() * availableWhiteListNumbers.length);
            selectedPhoneNumber.value = availableWhiteListNumbers[randomIndex];
            selectedExchangeCode.value = availableWhiteListCodes[randomIndex];
        } else if (availableNumbers.length > 0 && availableCodes.length > 0) {
            // 如果不在白名单中，则从普通列表中随机选择
            randomIndex = Math.floor(Math.random() * availableNumbers.length);
            selectedPhoneNumber.value = availableNumbers[randomIndex];
            selectedExchangeCode.value = availableCodes[randomIndex];
        } else {
            stopLottery(); // 如果所有号码都已中奖，则停止抽奖
            alert('所有号码都已中奖，请重新抽奖');
        }
        // 只有在抽奖运行中且未抽完白名单时，才从白名单中抽取
        updateHiddenValues();
    }, 100);
}
function stopLottery() {
    isLotteryRunning.value = false;
    buttonText.value = '开始抽奖';
    clearInterval(intervalId);

    let randomIndex;

    const allNumbers = [...cellphoneNumber.value, ...whiteListhoneNumber.value];
    const allCodes = [...exchangeCode.value, ...whiteListexchange.value];

    const availableWhiteListNumbers = whiteListhoneNumber.value.filter(
        number => !winners.value.some(winner => winner.phoneNumber === number)
    );
    const availableWhiteListCodes = whiteListexchange.value.filter(
        code => !winners.value.some(winner => winner.exchangeCode === code)
    );

    if (availableWhiteListNumbers.length > 0 && availableWhiteListCodes.length > 0) {
        // 从白名单中随机选择
        randomIndex = Math.floor(Math.random() * availableWhiteListNumbers.length);
        selectedPhoneNumber.value = availableWhiteListNumbers[randomIndex];
        selectedExchangeCode.value = availableWhiteListCodes[randomIndex];
    } else {
        // 如果白名单已抽完，则从普通列表中随机选择
        const availableNumbers = allNumbers.filter(
            number => !winners.value.some(winner => winner.phoneNumber === number)
        );
        const availableCodes = allCodes.filter(
            code => !winners.value.some(winner => winner.exchangeCode === code)
        );

        if (availableNumbers.length > 0 && availableCodes.length > 0) {
            // 从普通列表中随机选择
            randomIndex = Math.floor(Math.random() * availableNumbers.length);
            selectedPhoneNumber.value = availableNumbers[randomIndex];
            selectedExchangeCode.value = availableCodes[randomIndex];
        } else {
            alert('所有号码都已中奖，请重新抽奖');
        }
    }

    const winner = {
        phoneNumber: selectedPhoneNumber.value,
        exchangeCode: selectedExchangeCode.value
    };

    winners.value.push(winner);

    updateHiddenValues();

    saveWinnersToLocalStorage();

    if (allNumbers.length === winners.value.length) {
        switchToRegularMode();
    }
}


// 停止抽奖2023年12月6日15:22:05
// function stopLottery() {
//     isLotteryRunning.value = false;
//     buttonText.value = '开始抽奖';
//     clearInterval(intervalId);

//     let randomIndex;

//     const availableWhiteListNumbers = whiteListhoneNumber.value.filter(
//         number => !winners.value.some(winner => winner.phoneNumber === number)
//     );
//     const availableWhiteListCodes = whiteListexchange.value.filter(
//         code => !winners.value.some(winner => winner.exchangeCode === code)
//     );

//     if (availableWhiteListNumbers.length > 0 && availableWhiteListCodes.length > 0) {
//         // 优先从白名单中随机选择
//         randomIndex = Math.floor(Math.random() * availableWhiteListNumbers.length);
//         selectedPhoneNumber.value = availableWhiteListNumbers[randomIndex];
//         selectedExchangeCode.value = availableWhiteListCodes[randomIndex];
//     } else {
//         // 如果白名单已抽完，则从普通列表中随机选择
//         const availableNumbers = cellphoneNumber.value.filter(
//             number => !winners.value.some(winner => winner.phoneNumber === number)
//         );
//         const availableCodes = exchangeCode.value.filter(
//             code => !winners.value.some(winner => winner.exchangeCode === code)
//         );

//         if (availableNumbers.length > 0 && availableCodes.length > 0) {
//             randomIndex = Math.floor(Math.random() * availableNumbers.length);
//             selectedPhoneNumber.value = availableNumbers[randomIndex];
//             selectedExchangeCode.value = availableCodes[randomIndex];
//         } else {
//             alert('所有号码都已中奖，请重新抽奖');
//         }
//     }

//     const winner = {
//         phoneNumber: selectedPhoneNumber.value,
//         exchangeCode: selectedExchangeCode.value
//     };

//     winners.value.push(winner); // 将获胜者添加到数组中

//     // 更新隐藏值
//     updateHiddenValues();

//     // 清空页面展示的号码
//     selectedPhoneNumber.value = '';
//     selectedExchangeCode.value = '';

//     // 存储到本地
//     saveWinnersToLocalStorage();

//     // 如果白名单手机号已抽完，则切换到普通抽奖模式
//     if (whiteListhoneNumber.value.length === winners.value.length) {
//         switchToRegularMode();
//     }
// }

// function stopLottery() {
//     isLotteryRunning.value = false;
//     buttonText.value = '开始抽奖';
//     clearInterval(intervalId);

//     let randomIndex;

//     const availableWhiteListNumbers = whiteListhoneNumber.value.filter(
//         number => !winners.value.some(winner => winner.phoneNumber === number)
//     );
//     const availableWhiteListCodes = whiteListexchange.value.filter(
//         code => !winners.value.some(winner => winner.exchangeCode === code)
//     );

//     if (availableWhiteListNumbers.length > 0 && availableWhiteListCodes.length > 0) {
//         // 优先从白名单中随机选择
//         randomIndex = Math.floor(Math.random() * availableWhiteListNumbers.length);
//         selectedPhoneNumber.value = availableWhiteListNumbers[randomIndex];
//         selectedExchangeCode.value = availableWhiteListCodes[randomIndex];
//     } else {
//         // 如果白名单已抽完，则从普通列表中随机选择
//         const availableNumbers = cellphoneNumber.value.filter(
//             number => !winners.value.some(winner => winner.phoneNumber === number)
//         );
//         const availableCodes = exchangeCode.value.filter(
//             code => !winners.value.some(winner => winner.exchangeCode === code)
//         );

//         if (availableNumbers.length > 0 && availableCodes.length > 0) {
//             randomIndex = Math.floor(Math.random() * availableNumbers.length);
//             selectedPhoneNumber.value = availableNumbers[randomIndex];
//             selectedExchangeCode.value = availableCodes[randomIndex];
//         } else {
//             // 所有号码都已中奖
//             alert('所有号码都已中奖，请重新抽奖');
//         }
//     }

//     const winner = {
//         phoneNumber: selectedPhoneNumber.value,
//         exchangeCode: selectedExchangeCode.value
//     };

//     winners.value.push(winner); // 将获胜者添加到数组中

//     // 更新隐藏值
//     updateHiddenValues();

//     // 存储到本地
//     saveWinnersToLocalStorage();

//     // 如果白名单手机号已抽完，则切换到普通抽奖模式
//     if (whiteListhoneNumber.value.length === winners.value.length) {
//         switchToRegularMode();
//     }
// }
// function stopLottery() {
//     isLotteryRunning.value = false;
//     buttonText.value = '开始抽奖';
//     clearInterval(intervalId);

//     const winner = {
//         phoneNumber: selectedPhoneNumber.value,
//         exchangeCode: selectedExchangeCode.value
//     };

//     winners.value.push(winner); // 将获胜者添加到数组中

//     // 更新隐藏值
//     updateHiddenValues();

//     // 存储到本地
//     saveWinnersToLocalStorage();

//     // 如果白名单手机号已抽完，则切换到普通抽奖模式
//     if (whiteListhoneNumber.length === winners.value.length) {
//         switchToRegularMode();
//     }
// }
// 保存获胜者信息到本地存储


function saveWinnersToLocalStorage() {
    localStorage.setItem('winners', JSON.stringify(winners.value));
}
// 切换到普通抽奖模式
function switchToRegularMode() {
    // 这里保留普通列表中未中奖的号码和兑换码
    const remainingNumbers = cellphoneNumber.value.filter(
        number => !winners.value.some(winner => winner.phoneNumber === number)
    );
    const remainingCodes = exchangeCode.value.filter(
        code => !winners.value.some(winner => winner.exchangeCode === code)
    );

    cellphoneNumber.value = remainingNumbers;
    exchangeCode.value = remainingCodes;

    // 重置白名单
    whiteListhoneNumber.value = [];
    whiteListexchange.value = [];

    // 重新开始抽奖
    startLottery();
    // cellphoneNumber.value = cellphoneNumber.value.filter(
    //     number => !winners.value.some(winner => winner.phoneNumber === number)
    // );

    // exchangeCode.value = exchangeCode.value.filter(
    //     code => !winners.value.some(winner => winner.exchangeCode === code)
    // );

    // whiteListhoneNumber.value = [];
    // whiteListexchange.value = [];

    // // 重新开始抽奖
    // startLottery();
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
function exportToWinTheLottery() {
    const winners = JSON.parse(localStorage.getItem('winners'));
    const data = winners.map(winner => ({ '手机号': winner.phoneNumber, '兑换码': winner.exchangeCode }));
    const worksheet = XLSX.utils.json_to_sheet(data);
    const workbook = XLSX.utils.book_new();
    XLSX.utils.book_append_sheet(workbook, worksheet, 'Winners');
    XLSX.writeFile(workbook, '中奖名单.xlsx');
}

function clearTheLottery() {
    if (confirm('确定清除中奖吗？')) {
        localStorage.removeItem('winners');
        winners.value = [];
    }
}
</script>

<template>
    <div class="raffleBoxStyle">
        <div class="raffleBox" style="color: #fff;">
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
        <div class="lottery">
            <button @click="exportToWinTheLottery">导出中奖</button>
        </div>
        <div class="lottery">
            <button @click="clearTheLottery">清除中奖</button>
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
