<template>
  <div>
    <div class="container">
      <div class="row">
        <div class="col-12 mb-5">
          <h1>Conversor de moedas usando VueJS</h1>
          <hr>
        </div>
      </div>
      <div class="row">
        <div class="col-12">
          <label for="send" class="textLeft">Digite abaixo o valor que você vai enviar:</label>
        </div>
        <div class="col-8 p-0">
          <input class="form-control form-control-lg" id="send" type="text" v-model="amount" @input="handleChange"/>
        </div>
        <div class="col-4 p-0">
          <select class="form-control form-control-lg stylSelect" v-model="origin" @change="originCurrencyHandle">
            <option
              v-for="currency in currenciesAvailable"
              v-bind:value="currency"
              v-bind:key="currency.index">
              {{currency}}
            </option>
          </select>
        </div>
      </div>
      <div class="row">
        <div class="col-12 m-5 text-left text-muted">
            <strong>
            <p>{{ origin }} 1 to {{ destiny }} {{ fxRate }}</p>
            <p>IOF(1,10%): {{ origin }} - {{ iofOverAmount }}</p>
            <p>Taxa administrativa: {{ origin }} - {{ fxOverAmount }}</p>
            </strong>
        </div>
      </div>
      <div class="row">
        <div class="col-12">
          <label for="send" class="textLeft">O valor abaixo é o que a outra pessoa vai receber:</label>
        </div>
        <div class="col-8 p-0">
          <input class="form-control form-control-lg" id="receive" type="text" v-model="convertedAmount" @input="handleChange"/>
        </div>
        <div class="col-4 p-0">
          <select class="form-control form-control-lg stylSelect" v-model="destiny" @change="destinyCurrencyHandle">
            <option
              v-for="currency in currenciesAvailable"
              v-bind:value="currency"
              v-bind:key="currency.index">
              {{currency}}
            </option>
          </select>
        </div>
      </div>
    </div>
  </div>
</template>

<script lang='ts'>
import { Component, Vue } from 'vue-property-decorator'

@Component
export default class CurrencyConverter extends Vue {
  private amount = 1000
  private convertedAmount = 0

  private currenciesAvailable = ['USD', 'BRL', 'EUR']

  private origin = this.currenciesAvailable[0]
  private lastOrigin = this.currenciesAvailable[0]

  private destiny = this.currenciesAvailable[1]
  private lastDestiny = this.currenciesAvailable[1]

  private handle = ''

  private iofOverAmount = 0
  private fxOverAmount = 0
  private fxRate = 0

  mounted () {
    this.convertAmount(this.amount, this.origin, this.destiny, 'mounted')
  }

  handleChange () {
    console.log('')
    this.convertAmount(this.amount, this.origin, this.destiny, 'handleChange')
  }

  originCurrencyHandle () {
    if (this.origin === this.destiny && this.handle === '') {
      if (this.lastOrigin === this.destiny) {
        this.destiny = this.lastDestiny
        this.lastOrigin = this.origin
        this.convertAmount(this.amount, this.origin, this.destiny, 'origin')
      } else {
        this.destiny = this.lastOrigin
        this.lastOrigin = this.destiny
        this.convertAmount(this.amount, this.origin, this.destiny, 'origin')
      }
    } else if (this.origin !== this.destiny && this.origin !== this.lastOrigin) {
      this.handle = this.origin
      this.convertAmount(this.amount, this.origin, this.destiny, 'origin')
    } else if (this.origin === this.destiny) {
      if (this.origin === this.destiny && this.destiny === this.handle) {
        this.destiny = this.lastOrigin
        this.convertAmount(this.amount, this.origin, this.destiny, 'origin')
      } else {
        this.destiny = this.handle
        this.handle = ''
        this.convertAmount(this.amount, this.origin, this.destiny, 'origin')
      }
    }
  }

  destinyCurrencyHandle () {
    if (this.destiny === this.origin && this.handle === '') {
      if (this.lastDestiny === this.origin) {
        this.origin = this.lastOrigin
        this.lastDestiny = this.destiny
        this.convertAmount(this.amount, this.origin, this.destiny, 'destiny')
      } else {
        this.origin = this.lastDestiny
        this.lastDestiny = this.origin
        this.convertAmount(this.amount, this.origin, this.destiny, 'destiny')
      }
    } else if (this.destiny !== this.origin && this.destiny !== this.lastDestiny) {
      this.handle = this.destiny
      this.convertAmount(this.amount, this.origin, this.destiny, 'destiny')
    } else if (this.destiny === this.origin) {
      if (this.destiny === this.origin && this.destiny === this.handle) {
        this.origin = this.lastOrigin
        this.convertAmount(this.amount, this.origin, this.destiny, 'destiny')
      } else {
        this.origin = this.handle
        this.handle = ''
        this.convertAmount(this.amount, this.origin, this.destiny, 'destiny')
      }
    }
  }

  convertAmount (amount: number, origin: string, destiny: string, convertFrom: string) {
    const calculateIofValue = (0.011 * amount)
    this.iofOverAmount = calculateIofValue
    const calculateFxValue = (0.01 * amount)
    this.fxOverAmount = calculateFxValue
    this.doConverssion(convertFrom)
  }

  doConverssion (convertFrom: string) {
    if (convertFrom === 'origin' || convertFrom === 'mounted' || convertFrom === 'handleChange') {
      if (this.origin === 'USD' && this.destiny === 'BRL') {
        this.setFxRateBasedOnOriginAndDestiny(5.2164)
      } else if (this.origin === 'BRL' && this.destiny === 'USD') {
        this.setFxRateBasedOnOriginAndDestiny(0.1917)
      } else if (this.origin === 'EUR' && this.destiny === 'BRL') {
        this.setFxRateBasedOnOriginAndDestiny(6.3970)
      } else if (this.origin === 'BRL' && this.destiny === 'EUR') {
        this.setFxRateBasedOnOriginAndDestiny(0.1563)
      } else if (this.origin === 'EUR' && this.destiny === 'USD') {
        this.setFxRateBasedOnOriginAndDestiny(1.2206)
      } else if (this.origin === 'USD' && this.destiny === 'EUR') {
        this.setFxRateBasedOnOriginAndDestiny(1.2206)
      }
    } else {
      if (this.destiny === 'USD' && this.origin === 'BRL') {
        this.setFxRateBasedOnOriginAndDestiny(5.2164)
      } else if (this.destiny === 'BRL' && this.origin === 'USD') {
        this.setFxRateBasedOnOriginAndDestiny(0.1917)
      } else if (this.destiny === 'EUR' && this.origin === 'BRL') {
        this.setFxRateBasedOnOriginAndDestiny(6.3970)
      } else if (this.destiny === 'BRL' && this.origin === 'EUR') {
        this.setFxRateBasedOnOriginAndDestiny(0.1563)
      } else if (this.destiny === 'EUR' && this.origin === 'USD') {
        this.setFxRateBasedOnOriginAndDestiny(1.2206)
      } else if (this.destiny === 'USD' && this.origin === 'EUR') {
        this.setFxRateBasedOnOriginAndDestiny(1.2206)
      }
    }
  }

  setFxRateBasedOnOriginAndDestiny (fxRate: number) {
    this.fxRate = fxRate
    const executeConverssion = (this.amount - this.iofOverAmount - this.fxOverAmount) * this.fxRate
    this.convertedAmount = executeConverssion
  }
}
</script>

<style scoped>

.textLeft {
  float: left !important
}

.stylSelect {
  background-color: #D3D3D3;
  color: #212121;
}

.stylSelect:hover {
  background-color: #D3D3D3;
  color: #212121;
}

.stylSelect:active {
  background-color: #D3D3D3;
  color: #212121;
}
</style>
