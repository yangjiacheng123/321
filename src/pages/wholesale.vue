<template>
  <div class="wholesale">
    <div class="banner"></div>
    <div class="show_product">
      <div class="bg"></div>
      <div class="product"></div>
      <div class="description">
        <p>太空树脂球床垫</p>
        <p>MZZ1-013</p>
        <p class="product_num">180x200x30cm</p>
        <p class="product_price">零售价：33999元</p>
      </div>
      <div class="price">
        <img src="../assets/images/prize.png" alt="">
      </div>
    </div>
    <div class="content">
      <p>
        <span v-if="isEnd">已</span>
        <span v-else>共</span>有 <strong>{{ num }}</strong>
        人参与拼团
      </p>
      <div class="countTime">
        <span>一</span>
        <p v-if="isEnd">还剩
          <span class="time day">{{ day }}</span>天
          <span class="time hour">{{ hour }}</span>时
          <span class="time minute">{{ minute }}</span>分
          <span class="time seconds">{{ seconds }}</span>秒
        </p>
        <p v-else>活动已结束</p>
        <span>一</span>
      </div>
      <div class="free">满1000人成团，并有一名客户<strong>免单</strong></div>
      <div class="rules">详情查看
        <span class="rules_details" @click="showBtn">活动规则>></span>
      </div>
    </div>
    <div class="free_customer">
      <h1>免单客户</h1>
      <div class="free_bg"></div>
      <div v-if="freeStatus">
        <div class="free_man"></div>
        <p>4月1日抽出一人免单</p>
      </div>
      <div class="free_person" v-else>
        <h5>{{ username }}</h5>
        <h5>{{ phone }}</h5>
      </div>
    </div>
    <div class="footer"></div>
    <div class="showrules" v-show="showStatus">
      <show-rules :changVal='changVal'/>
    </div>
  </div>
</template>

<script>
import SockJS from 'sockjs-client';
import Stomp from 'stompjs';
import {IndexModel} from '../utils/index'
const indexModel = new IndexModel()
import ShowRules from '../components/showRules';

import {countNumber} from '../utils/common'
import {phoneDisplay, nameDisplay} from '../utils/common'
export default {
  components: { ShowRules },
  data() {
    return {
      showStatus: '',
      freeStatus: true,
      isEnd: true,
      day: '',
      hour: '',
      minute: '',
      seconds: '',
      num: '',
      username: '',
      phone: '',
      key: true
    }
  },
  watch: {
    // min() {
    //   console.log(11111111)
    //   this.getFreeMan()
    // }
  },
  mounted() {
    this.getFreeMan()
    this.time()
    this.connection()
  },
  methods: {
    //websocket
    connection() {
      // let sock = new SockJS("http://10.11.8.207/endpointChat");
      let sock = new SockJS("https://derucci.net/endpointChat");
      this.stompClient = Stomp.over(sock);
      this.stompClient.connect({}, () => {
        this.stompClient.subscribe('/topic/groupBooking/count', (res) => {
          if(res.body) {
            console.log('resbody',res.body)
            this.countNum(res.body)
          }
        });
      });
    },
    //判断人数
    countNum(joinNum) {
      var tempNum = countNumber()
      if(tempNum) {
         if(joinNum < tempNum) {
          this.num = tempNum
        }else {
          this.num = joinNum
        }
      }else {
        if(joinNum < 1000) {
          this.num = 1030 
        }else {
          this.num = joinNum
        }
      }
    },
    //倒计时
    time() {
      let endTime = new Date('2019/03/24 23:59:59').getTime() + 1000;
      let interval = null;
      interval = setInterval(() => {
        let remainingTime = endTime - Date.now(); // 剩余毫秒
        if (remainingTime >= 0) {
          this.day = Math.floor(remainingTime / 1000 / 60 / 60 / 24);
          this.hour = Math.floor(remainingTime / 1000 / 60 / 60 % 24);
          this.minute = Math.floor(remainingTime / 1000 / 60 % 60);
          this.seconds = Math.floor(remainingTime / 1000 % 60);
        } else {
          clearInterval(interval);
          this.isEnd = false
        }
      }, 0);
    },
    //4/1抽奖
    getFreeMan() {
      let year = new Date().getFullYear()
      let day = new Date().getDate()
      let mouth = new Date().getMonth() + 1
      let hour = new Date().getHours()
      if(year == 2019 && mouth == 4 && day == 1 && hour == 11) {
        this.getPrizes()
      }else {
        return 
      }
    },
    getPrizes() {
      let date = '2019-04-01 11'
      let type = '321-1'
      indexModel.getPrizes(date, type).then(res => {
        console.log('data',res.data[0].username)
        if(res.data[0].username) {
          this.freeStatus = false
          this.username = nameDisplay(res.data[0].username)
          this.phone = phoneDisplay(res.data[0].phone)
        }
      })
    },
    //展示活动规则
    showBtn() {
      this.showStatus = true
    },
    //关闭规则
    changVal(val) {
      this.showStatus = val
    }
  }
}
</script>

<style lang="scss" scoped>
.wholesale {
  position: relative;
  width: 100vw;
  min-height: 100vh;
  background-color: rgba(6, 23, 41, 1);
  .banner {
    background: url(../assets/images/banner.png) no-repeat center;
    background-size: 100% 100%;
    width: 100%;
    height: 50vw;
  }
  .show_product {
    width: 100%;
    text-align: center;
    margin-top: 6.66vw;
    position: relative;
    padding-bottom: 4.53vw;
    border-bottom: 1px solid rgba(0, 2, 4, 1);
    box-shadow:0 2px 0 0 rgba(46,116,190,0.1);
    .bg {
      background: url(../assets/images/head.png) no-repeat center;
      background-size: 100% 100%;
      width: 90.66%;
      height: 34.66vw;
      margin:0 auto;
    }
    .product {
      background: url(../assets/images/bed.png) no-repeat center;
      background-size: 100% 100%;
      width: 46.13vw;
      height: 18.13vw;
      position: absolute;
      top: -3.2vw;
      left: 7.67vw;
    }
    .price {
      background: url(../assets/images/prizeBg.png) no-repeat center;
      background-size: 100% 100%;
      width: 54.8vw;
      height: 13.06vw;
      position: absolute;
      bottom: 2.8vw;
      left: 3vw;
      img {
        width: 44.13vw;
        height: 6.26vw;
        margin-top: 2.8vw;
      }
    }
    .description {
      position: absolute;
      top: 3.6vw;
      right: 8vw;
      color: #24508d;
      font-size: 4.8vw;
      font-weight: bold;
      text-align: right;
      .product_num {
        font-size: 3.2vw;
        font-weight: normal; 
      }
      .product_price {
        font-size: 3.2vw;
        color: #0f81f7
      }
    }
  }
  .content {
    z-index: 1000;
    padding-top: 4.53vw;
    padding-bottom: 3.73vw;
    color: #fff;
    font-weight: bold;
    font-size: 4.8vw;
    text-align: center;
    border-bottom: 1px solid rgba(0, 2, 4, 1);
    box-shadow:0 2px 0 0 rgba(46,116,190,0.1);
    strong {
      color: #89e3fa;
      font-size: 6.4vw;
    }
    .countTime {
      display: flex;
      justify-content: center;
      align-items: center;
      span {
        font-weight: bold;
        color:#0c6ecc;
        margin-left: 2vw;
        margin-right: 2vw;
      }
      p {
        font-size: 3.73vw;
        font-weight: normal;
        color: #fff;
      }
      .time {
        background:#a2e8fc;
        padding: 1vw 1.6vw;
        border-radius: .53vw;
        font-size: 4.26vw;
        color:#06192c;
      }
    }
    .free {
      font-weight: normal;
      font-size: 3.73vw;
    }
    .rules {
      font-weight: normal;
      font-size: 3.73vw;
      .rules_details {
        font-size: 4.8vw;
        color: #89e3fa;
        border-bottom: 1px solid #89e3fa
      }
    }
  }
  .free_customer {
    position: relative;
    margin-top: 4.06vw;
    height: 50vw;
    width: 100%;
    z-index: 99;
    h1 {
      line-height: 1.8em;
      text-align: center;
      font-size: 3.73vw;
      font-weight: bold;
      color:rgba(248,248,248,1);
      text-shadow: 0px 0.4vw 2px rgba(26,39,76,0.4);
      -webkit-text-stroke: 1px undefined;
      background: linear-gradient(0deg,rgba(101,177,255,1) 0%, rgba(236,245,254,1) 34.5703125%, rgba(101,177,255,1) 67.8955078125%, rgba(239,245,252,1) 100%);
      -webkit-background-clip: text;
      -webkit-text-fill-color: transparent
    }
    .free_bg {
      background: url(../assets/images/line.png) no-repeat center;
      background-size: 100% 100%;
      width: 25.33vw;
      height: 2.93vw;
      margin: 0 auto;
    }
    .free_man {
      background: url(../assets/images/photo.png) no-repeat center;
      background-size: 100% 100%;
      width: 11.73vw;
      height: 11.73vw;
      margin: 2vw auto;
    }
    h5 {
      color: #f8f8f8;
      font-size: 4.8vw;
      text-align: center;
    }
    p {
      color: rgba(248, 248, 248, 0.3);
      text-align: center;
    }
  }
  .footer {
    background: url(../assets/images/footer.png) no-repeat center;
    background-size: 100% 100%;
    width: 100%;
    height: 83.33vw;
    position: absolute;
    bottom: 0;
    left: 0;
    pointer-events:none;
  }
  .showrules {
    position: fixed;
    top: 0;
    left: 0;
    width: 100vw;
    height: 100vh;
    background: rgba(0,0,0,0.5);
    z-index: 999;
  }
}
</style>
