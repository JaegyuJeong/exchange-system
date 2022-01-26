<template>
  <div id="app">
    <div class="header">환율 계산</div>
    <div class="body">
      <div class="body-container">
        <span class="body-container__title">송금국가: </span>
        <span class="body-container__content">미국(USD)</span>
      </div>
      <div class="body-container">
        <span class="body-container__title">수취국가: </span>
        <select class="body-container__content" v-model="exchangeCurrency" @change="getExchangeRate">
          <option value="" disabled selected>국가선택</option>
          <option value="USDKRW">한국(KRW)</option>
          <option value="USDJPY">일본(JPY)</option>
          <option value="USDPHP">필리핀(PHP)</option>
        </select>
      </div>
      <div class="body-container">
        <span class="body-container__title">환율: </span>
        <span class="body-container__content">{{ exchangeRate }}
          <span style="margin-left: 5px">{{ exchangeCurrency.substr(3) }}/USD</span>
        </span>
      </div>
      <div class="body-container">
        <span class="body-container__title">송금액: </span>
        <span class="body-container__content">
         <input type="numbers" v-model="inputDollar" />
          USD
        </span>
      </div>
    </div>
    <div class="footer">
      <div class="footer-button" @click="computedAmount">
        <span>Submit</span>
      </div>
      <div class="footer-result" v-if="convertedAmount">
        <span>수취금액은 {{ convertedAmount |currencyFormat }} {{ exchangeCurrency.substr(3) }} 입니다.</span>
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";
import apiKey from "./assets/data/api_key.json";

const YOUR_ACCESS_KEY = apiKey.currencyKey;
export default {
  data() {
    return {
      exchangeCurrency: "", // 환전통화(KRW, JPY, PHP)
      exchangeRate: null,    // 환율
      inputDollar: null,    // 송금액
      convertedAmount: null // 환전된 금액
    };
  },
  methods: {
    async getExchangeRate() {
      if (this.exchangeCurrency) {
        let res = await axios.get(`http://api.currencylayer.com/live?access_key=${YOUR_ACCESS_KEY}`);
        this.exchangeRate = res.data.quotes[this.exchangeCurrency];
      } else {
        alert("수취국가를 선택해주세요");
      }
    },
    computedAmount() {
      if (this.exchangeCurrency) {
        if (this.inputDollar && this.inputDollar >= 0 && this.inputDollar <= 10000) {
          this.convertedAmount = this.inputDollar * this.exchangeRate;
        } else {
          alert("송금액이 바르지 않습니다");
        }
      }else{
        alert("수취국가를 선택해주세요");
      }
    }
  },
  filters: {
    currencyFormat(value) {
      const option = { maximumFractionDigits: 2, minimumFractionDigits: 2 };
      return value.toLocaleString('ko-KR', option);
    }
  }
};
</script>

<style lang="scss">
#app {
  width: 300px;
}

.header {
  font-weight: bold;
  font-size: 40px;
  margin-bottom: 10px;
}

.body {
  margin-bottom: 10px;

  &-container {
    padding: 5px;
    display: flex;
    align-items: center;

    &__title {
      font-weight: bold;
      font-size: 20px;
      margin-right: 5px;
      width: 80px;
    }

    &__content {
      display: flex;
      justify-content: center;
      align-items: center;

      input {
        margin-right: 5px;
      }
    }
  }
}

.footer {
  width: 100%;
  font-weight: bold;
  font-size: 15px;
  color: #005c3e;

  &-button {
    width: 100px;
    height: 50px;
    border-radius: 8px;
    border: 3px solid #005c3e;


    display: flex;
    justify-content: center;
    align-items: center;
    margin-bottom: 10px;
  }
}
</style>
