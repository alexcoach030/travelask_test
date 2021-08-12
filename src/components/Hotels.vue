<template>
  <div @click="search" class="wrap">
    <div class="content">
      <div class="filters">
        <div class="filters__left">
          <div class="filters__country">
            <details open>
              <summary><span>Поиск по странам и городам</span></summary>
              <form>
                <label>
                  <input @focus="search" id="search1" type="text" placeholder="Введите страну, например, Россия" v-model="searchCountry1">
                </label>
                <div class="searchlist" v-if="searchHelper1">
                  <div @click="getCountryFiltered" id="searchCountry1" class="searchlist__country" v-for="country in countryList.slice(0,10)" :key="country">{{ country }}</div>
                </div>
              </form>
            </details>
          </div>
        </div>
        <div class="filters__main">
          <div class="filters__country">
            <details open>
              <summary><span>Поиск по странам и городам</span></summary>
              <form>
                <label>
                  <input @focus="search" id="search2" type="text" placeholder="Введите страну, например, Россия" v-model="searchCountry2">
                </label>
                <div class="searchlist" v-if="searchHelper2">
                  <div @click="getCountryFiltered" id="searchCountry2" class="searchlist__country" v-for="country in countryList.slice(0,10)" :key="country">{{ country }}</div>
                </div>
              </form>
            </details>
          </div>
          <div class="filters__stars">
            <details open>
              <summary><span>Звезды</span></summary>
              <form class="stars">
                <label class="stars__label"><input type="checkbox" value="0" id="no-stars" v-model="stars"><span>Без звезд</span></label>
                <label class="stars__label"><input type="checkbox" value="1" id="1-star" v-model="stars"><span>1 звезда</span></label>
                <label class="stars__label"><input type="checkbox" value="2" id="2-star" v-model="stars"><span>2 звезды</span></label>
                <label class="stars__label"><input type="checkbox" value="3" id="3-star" v-model="stars"><span>3 звезды</span></label>
                <label class="stars__label"><input type="checkbox" value="4" id="4-star" v-model="stars"><span>4 звезды</span></label>
                <label class="stars__label"><input type="checkbox" value="5" id="5-star" v-model="stars"><span>5 звезд</span></label>
              </form>
            </details>
          </div>
          <div class="filters__type">
            <details open>
              <summary><span>Тип</span></summary>
              <div class="filters__buttons">
                <button @click="switchHotels" class="filters__button" v-bind:class="{ 'filters__button_active':this.showHotels }">Отели</button>
                <button @click="switchApartments" class="filters__button" v-bind:class="{ 'filters__button_active':this.showApartments }">Апартаменты</button>
              </div>
            </details>
          </div>
          <div class="filters__price">
            <details open>
              <summary><span>Цена</span></summary>
              <div class="filters__slider">
                <vue-slider :slider-style="sliderStyle"
                            :tooltip-dir="tooltipDir"
                            :min="0"
                            :max="15000"
                            :tooltip-style="tooltipStyle"
                            :process-style="processStyle"
                            v-model="rangeValue">
                  <template v-slot:dot="{ value, focus }">
                    <div :class="['filters__dot', { focus }]"></div>
                  </template>
                </vue-slider>
              </div>
            </details>
          </div>
          <div class="filters__reset">
            <button @click="resetFilters" class="filters__resetbtn">Сбросить все фильтры</button>
          </div>
        </div>
      </div>
      <div class="hotels">
        <div class="hotels__empty" v-if="render.length === 0">
          <p>Записей не найдено</p>
        </div>
        <div class="hotel" v-for="hotel in render" :key="hotel">
          <div class="hotel__image">
            <picture>
              <source type="image/webp" :srcset="require(`@/assets/${hotel.name}.webp`)">
              <img :src="require(`@/assets/${hotel.name}.jpg`)" :alt="hotel.name">
            </picture>
          </div>
          <div class="hotel__information">
            <p class="hotel__address">{{ hotel.address }}</p>
            <h1 class="hotel__name">{{ hotel.name }}</h1>
            <div class="hotel__stars">
              <div class="stars__imageholder">
                <img class="stars__image" v-for="item in hotel.stars" :key="item" src="../assets/star.svg" alt="star">
              </div>
              <p class="stars__tag"> {{ getStars(hotel.stars) }}</p>
              <div v-html="getType(hotel.type)"></div>
            </div>
            <p class="hotel__description">{{ hotel.description }}</p>
          </div>
          <div class="hotel__others">
            <div v-if="hotel.reviews_amount === 0" class="hotel__review_nomarks">
              <img src="../assets/noreviewstar.svg" alt="no review">
              <p>Нет оценок.</p>
            </div>
            <div v-if="hotel.reviews_amount !== 0" class="hotel__review">
              <div class="hotel__rating">
                <img class="hotel__ratingimg" src="../assets/reviewstar.svg" alt="review">
                <p class="hotel__ratio">{{ hotel.rating }}</p>
                <p class="hotel__mark">{{ getMark(hotel.rating) }}</p>
                <p class="hotel__reviewAmount">{{ getAmount(hotel.reviews_amount) }}</p>
              </div>
              <p class="hotel__reviewtext">"{{ hotel.last_review }}"</p>
            </div>
            <div class="hotel__booking">
              <p class="hotel__price">от {{ Math.floor(hotel.min_price) }} <span v-if="hotel.currency === 'RUR'">₽</span></p>
              <button class="hotel__bookingbtn">Забронировать</button>
            </div>
          </div>
        </div>
        <div class="hotels__next">
          <button v-if="render.length !== 0" @click="getMoreHotels" class="hotels__nextbtn">Показать еще отели</button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import VueSlider from 'vue-slider-component'
import 'vue-slider-component/theme/antd.css'
import hotels from '../../hotels.json'

export default {
  name: "Hotels",
  components: {
    VueSlider
  },
  data(){
    return{
      rangeValue: [0, 15000],
      searchCountry1: '',
      searchCountry2: '',
      showHotels: true,
      showApartments: true,
      stars: [],
      hotels: hotels.hotels,
      filtered: true,
      searchHelper1: false,
      searchHelper2: false,
      countryList: [],
      step: 6,
    }
  },
  created() {
    this.railStyle = {
      backgroundColor: '#DFDACE',
      height: '3px',
    }
    this.bgStyle = {
      backgroundColor: '#DFDACE',
      height: '3px',
    }
    this.tooltipStyle = {
      backgroundColor: '#212121',
      borderRadius: '2px',
      height: '22px',
      padding: '0',
      fontSize: '13px',
      lineHeight: '22px',
      marginRight: '35px',
      width: '74px',
      textAlign: 'center',
      marginBottom: '4px'
    }
    this.processStyle = {
      backgroundColor: '#FF4641',
      height: '3px',
    }
  },
  computed: {
    render: function (){
      let data = JSON.parse(JSON.stringify(this.hotels));
      data = data.filter(this.filterPrice);
      data = data.filter(this.filterStars);
      data = data.filter(this.filterType);
      data = data.filter(this.filterCountry);
      this.getCountrylist(data);
      return data;
    }
  },
  methods: {
    switchHotels: function (){
      this.showHotels = !this.showHotels;
    },
    switchApartments: function (){
      this.showApartments = !this.showApartments;
    },
    resetFilters: function (){
      this.showHotels = true;
      this.searchCountry1 = '';
      this.searchCountry2 = '';
      this.stars = [];
      this.rangeValue = [0, 15000];
    },
    getStars: function (stars){
      if (stars == 0) return 'Без звезд';
      if (stars === 1) return '1 звезда';
      if (stars > 1 && stars < 5) return `${stars} звезды`;
      else return '5 звезд';
    },
    getType: function (type){
      return `<div class="filters__typeattr"><span>${type}</span></div>`;
    },
    getMark: function (value){
      if (value >= 5) return 'Отлично';
      if (value >=4) return 'Хорошо';
      if (value >=3) return 'Удовлетворительно';
      if (value < 3) return 'Плохо';
    },
    getAmount: function (amount){
      let value = amount.toString().split('').pop();
      let rest = Math.floor(amount/10);
      if (rest.toString().split('').pop() == 1) {
        return `${amount} отзывов`;
      }else {
        if (value === 1) return '1 отзыв';
        if (value === 0 || value > 4) return `${amount} отзывов`;
        else return `${amount} отзыва`;
      }
    },
    filterPrice: function (hotel){
      return hotel.min_price > this.rangeValue[0] && hotel.min_price < this.rangeValue[1];
    },
    filterStars: function (hotel){
      if (this.stars.length === 0) return true;
      let filter = this.stars.toString();
      return filter.includes(hotel.stars);
    },
    filterType: function (hotel){
      if (this.showApartments && this.showHotels) return true;
      if (this.showApartments) return hotel.type === 'Апартаменты';
      if (this.showHotels) return hotel.type === 'Отель';
    },
    filterCountry: function (hotel){
      let value = '';
      if (this.searchCountry1 !== ''){
        this.searchCountry2 = '';
        value = this.searchCountry1.toLowerCase();
      }
      if (this.searchCountry2 !== ''){
        this.searchCountry1 = '';
        value = this.searchCountry2.toLowerCase();
      }
      return hotel.address.toLowerCase().indexOf(value) > -1;
    },
    getCountrylist: function (data){
      let countrylist = [];
      for (let hotel of data){
        let country = hotel.country;
        if (!countrylist.includes(country)){
          countrylist.push(country);
        }
      }
      this.countryList = countrylist;
    },
    getCountryFiltered: function(event){
      if (event.target.id === 'searchCountry1'){
        this.searchCountry1 = event.target.innerText;
      }
      if (event.target.id === 'searchCountry2'){
        this.searchCountry2 = event.target.innerText;
      }
    },
    search: function (event){
      if (event.target.id === "search1" && this.searchCountry2 !== ''){
        this.searchCountry2 = '';
      }
      if (event.target.id === 'search2' && this.searchCountry1 !== ''){
        this.searchCountry1 = '';
      }
      if (event.target.id === 'search1'){
        this.searchHelper1 = true;
        this.searchHelper2 = false;
      }
      if (event.target.id === 'search2'){
        this.searchHelper1 = false;
        this.searchHelper2 = true;
      }
      if (event.target.id !== 'search1' && event.target.id !== 'search2'){
        this.searchHelper1 = false;
        this.searchHelper2 = false;
      }
    },
    getMoreHotels: function (){
      this.step = this.step + 6;
    }
  }
}
</script>

<style lang="less">
.content{
  display: flex;
  flex-wrap: nowrap;
  justify-content: flex-start;
  width: 1920px;
  margin: 0 auto;
  padding-top: 56px;
  padding-bottom: 85px;
}
.hotels{
  width: 938px;
  margin-left: 21px;
  display: flex;
  flex-direction: column;
}
summary {
  display: block;
  position: relative;
  border-bottom: 1px solid #F3EFEA;
  height: 65px;
  line-height: 65px;
  cursor: pointer;
  span{
    padding: 0 21px;
    font-weight: 500;
    font-size: 15px;
    line-height: 17px;
    color: #000000;
  }
}
summary::-webkit-details-marker {
  display: none;
}

summary::after {
  content: url("../assets/stroke.svg");
  padding-right: 0.5em;
  place-content: center;
  position: absolute;
}

details[open] > summary::after {
  content: url("../assets/strokedown.svg");
  position: absolute;
}
.checkbox{
  position: absolute;
  z-index: -1;
  opacity: 0;
}
.stars{
  display: flex;
  flex-direction: column;
  align-items: flex-start;
  margin: 30px 21px;
  .stars__label{
    margin-bottom: 21px;
    cursor: pointer;
    width: 100%;
  }
}
.stars__label>input{
  .checkbox;
}
.stars__label>span {
  display: inline-flex;
  align-items: center;
  user-select: none;
  font-size: 15px;
  line-height: 28px;
  color: #000000;
}
.stars__label>span::before{
  content: '';
  display: inline-block;
  width: 20px;
  height: 20px;
  flex-shrink: 0;
  flex-grow: 0;
  border: 2px solid #D5CDBE;
  border-radius: 2px;
  margin-right: 0.5em;
  background-repeat: no-repeat;
  background-position: center center;
  background-size: 70% 70%;
}
.stars__label>input:not(:disabled):not(:checked)+span:hover::before {
  border-color: #000000;
}
.stars__label>input:not(:disabled):active+span::before {
  border-color: #FF4641;
}
.stars__label>input:checked+span::before {
  background-color: #FF4641;
  background-image: url("../assets/checked.svg");
  border-color: #FF4641;
}
.stars__label>input:disabled+span::before {
  background-color: #BEC5CC;
}
.filters{
  display: flex;
  width: 651px;
  justify-content: space-around;
  margin-left: 30px;
}
.filters__left{
  width: 300px;
}
.filters__country{
  width: 100%;
  text-align: left;
  background-color: #FFFFFF;
  outline: 1px solid #F3EFEA;
  margin-bottom: 20px;
  form{
    width: 100%;
  }
  input{
    height: 40px;
    line-height: 40px;
    width: 228px;
    padding: 0 15px;
    margin: 30px 21px;
    color: #837F78;
    position: relative;
  }
}
.filters__main{
  width: 300px;
  display: flex;
  flex-direction: column;
  align-items: flex-start;
  margin-left: 21px;
}
.filters__stars{
  display: flex;
  flex-direction: column;
  width: 100%;
  text-align: left;
  outline: 1px solid #F3EFEA;
  margin-bottom: 30px;
  max-height: 415px;
}
.filters__type{
  text-align: left;
  width: 100%;
  outline: 1px solid #F3EFEA;
  margin-bottom: 20px;
}
.filters__price{
  text-align: left;
  width: 100%;
  outline: 1px solid #F3EFEA;
  margin-bottom: 30px;
}
.filters__slider{
  margin: 45px 32px 30px 21px;
  cursor: pointer;
}
.button{
  outline: none;
  cursor: pointer;
  border: none;
}
.filters__button{
  .button;
  background-color: #F9F7F2;
  border-radius: 50px;
  height: 40px;
  font-size: 15px;
  line-height: 40px;
  color: #000000;
  padding: 0 15px;
  margin-right: 8px;
}
.filters__button_active{
  background-color: #FDE283;
}
.filters__buttons{
  margin: 30px 21px;
}
.filters__reset{
  width: 100%;
  text-align: center;
}
.filters__resetbtn{
  .button;
  background-color: transparent;
  font-size: 15px;
  line-height: 28px;
  color: #0E0E0E;
  position: relative;
}
.filters__resetbtn::before{
  content: url("../assets/reset.svg");
  position: absolute;
  left: -24px;
  top: 2px;
}
.filters__dot {
  border: 5px solid #FF4641;
  height: 15px;
  width: 15px;
  border-radius: 15px;
  background-color: #FFFFFF;
  margin-top: -6px;
}
.vue-slider-dot-tooltip-inner::after {
  left: 81%;
}
.vue-slider-dot-tooltip-text::after {
  content: ' ₽';
}
.hotel{
  width: 100%;
  height: 290px;
  border-bottom: 1px solid #F3EFEA;;
  margin-bottom: 29px;
  display: flex;
  justify-content: flex-start;
  padding-bottom: 28px;
  p, h1, h2{
    margin: 0;
    padding: 0;
  }
}
.hotel__image{
  height: 100%;
  width: 280px;
}
.hotel__information{
  height: 100%;
  width: 385px;
  margin-left: 23px;
  text-align: left;
}
.hotel__others{
  height: 100%;
  width: 237px;
  margin-left: 13px;
  display: flex;
  flex-direction: column;
  justify-content: space-between;
}
.hotel__address{
  font-size: 13px;
  line-height: 17px;
  color: #515151;
  margin-bottom: 6px!important;
}
.hotel__name{
  font-weight: 500;
  font-size: 21px;
  line-height: 24px;
  color: #212121;
  margin-bottom: 18px!important;
}
.hotel__description{
  font-size: 14px;
  line-height: 20px;
  color: #000000;
  max-height: 190px;

}
.hotel__stars{
  display: flex;
  flex-wrap: nowrap;
  margin-bottom: 18px;
}
.filters__typeattr{
  outline: 1px solid #D4D4D4;
  padding: 0 7px;
  height: 20px;
  font-size: 13px;
  line-height: 17px;
  color: #515151;
  margin-left: 7px;
}
.stars__tag{
  font-size: 13px;
  line-height: 17px;
  color: #212121;
}
.stars__image{
  margin-right: 4px;
}
.stars__imageholder{
  margin-right: 8px;
}
.hotel__review{
  padding: 20px 27px 22px 25px;
  background-color: #F9F7F2;
  max-height: 220px;
}
.hotel__rating{
  display: flex;
  flex-wrap: nowrap;
  font-weight: 500!important;
  font-size: 14px!important;
  line-height: 17px!important;
  color: #000000!important;
  margin-bottom: 18px;
}
.hotel__ratingimg{
  margin-right: 3px!important;
}
.hotel__ratio{
  margin-right: 12px!important;
  color: #212121!important;
}
.hotel__mark{
  margin-right: 12px!important;
}
.hotel__reviewtext{
  font-size: 13px;
  line-height: 17px;
  color: #515151;
  text-align: left;
}
.hotel__review_nomarks{
  .hotel__review;
  display: flex;
  flex-wrap: nowrap;
  img{
    margin-right: 8px;
  }
}
.hotel__booking{
  display: flex;
  flex-wrap: nowrap;
  justify-content: space-between;
}
.hotel__bookingbtn{
  .button;
  width: 140px;
  height: 50px;
  background-color: #FF4641;
  border-radius: 2px;
  line-height: 50px;
  font-weight: 500;
  font-size: 14px;
  color: #FFFFFF;
  &:hover{
    background-color: #FF4627;
  }
}
.hotel__price{
  font-weight: 500;
  font-size: 17px;
  height: 50px;
  line-height: 50px;
  color: #000000;
}
.searchlist{
  margin: -30px 21px 0 21px;
  width: 262px;
  background-color: #FFFFFF;
  box-shadow: 0 10px 30px rgba(31, 21, 1, 0.1);
  border-radius: 2px;
  position: absolute;
  font-size: 15px;
  line-height: 20px;
  color: #000000;
  z-index: 1;
}
.searchlist__country{
  padding: 25px 30px;
  cursor: pointer;
  &:hover{
    background-color: #F9F7F2;
  }
}
.hotels__next{
  margin-top: 3px;
}
.hotels__nextbtn{
  .button;
  font-size: 15px;
  line-height: 17px;
  font-weight: 700;
  text-align: center;
  color: #000000;
  border: 2px solid #F0EADE;
  width: 199px;
  height: 50px;
  border-radius: 2px;
  background-color: #FFFFFF;
}
</style>