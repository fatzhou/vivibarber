<template>
  <div class="container dingdan-wrap yuyue-wrap">
      <div class="wrap">
          <div class="weui-form-preview" v-for="item, index in orderList">
              <div class="weui-form-preview__hd">
                  <div class="weui-form-preview__item">
                      <label class="weui-form-preview__label">预约号</label>
                      <span class="weui-form-preview__value">{{item.orderno}}</span>
                  </div>
              </div>
              <div class="weui-form-preview__bd">
                  <div class="weui-form-preview__item">
                      <label class="weui-form-preview__label">下单时间</label>
                      <span class="weui-form-preview__value">{{item.time}}</span>
                  </div>
                  <div class="weui-form-preview__item">
                      <label class="weui-form-preview__label">预约理发师</label>
                      <span class="weui-form-preview__value">{{item.detail[0].name}}</span>
                  </div>
                  <div class="weui-form-preview__item">
                      <label class="weui-form-preview__label">联系用户</label>
                      <span class="weui-form-preview__value"><i class="iconfont-dasan-16"></i><span>拨打</span></span>
                  </div>
                  <div class="weui-form-preview__item">
                      <label class="weui-form-preview__label">用户已等待</label>
                      <span class="weui-form-preview__value"> <span class="statu sta1">30分钟</span></span>
                  </div>
              </div>
              <div class="weui-form-preview__ft">
                  <a class="weui-btn weui-btn_primary " @click="dealOrder(item.orderno, index)" v-if="item.status == 0 || item.status == 1 || item.status == 2" href="javascript:" id="">通知Ta到店</a>
                  <a class="weui-btn weui-btn_primary weui-btn_disabled" v-else href="javascript:" id="">已通知</a>
              </div>
          </div>
      </div>
  </div>
</template>

<script>
  import util from '../assets/js/util.js'

export default {
  name: 'OrdreList',
  data () {
    return {
      url: util.api.host + util.api.queryOrder,
      dealUrl: util.api.host + util.api.dealOrder,
      // date: '20170620',
      pageno: 1,
      pagesize: 10,
      orderList: []
    }
  },

  activated() {
    this.queryOrderInfo();
  },

 methods: {
    dealOrder(orderno, index) {
      var postData = {
        openid: window.info.openid,
        token: window.info.token,
        shopid: window.info.shopid,
        orderno: orderno
      };

      this.$http.post(this.dealUrl, postData)
      .then((res)=>{
        var data = res.body;
        if(data.code == 0) {
          this.orderList[index].status = 3;
          this.orderList = this.orderList.filter(function() {
            return true;
          })
          // this.$set(this.orderList[index], status, 3);

        } else {
          alert(data.msg);
        }
      });
    },
    computeTime(st) {
      var dt = new Date(st);
      var year = dt.getFullYear(),
          month = dt.getMonth() + 1,
          day = dt.getDay(),
          hour = ('00' + dt.getHours()).slice(-2),
          minutes = ('00' + dt.getMinutes()).slice(-2),
          seconds = ('00' + dt.getSeconds()).slice(-2);
      return year + '年' + month + '月' + day + '日' + ' ' + [hour, minutes, seconds].join(':');
    },
    queryOrderInfo() {
      var postData = {
        openid: window.info.openid,
        token: window.info.token,
        shopid: window.info.shopid,
        pageno: this.pageno,
        pagesize: this.pagesize
      };

      this.$http.post(this.url, postData)
      .then((res)=>{
        var data = res.body;
        console.log(data,'orderlist')
        if(data.code == 0) {
          var orderList = data.orderlist || [];
          orderList.forEach((item)=>{
            item.time = this.computeTime(item.createtime * 1000);
            if(typeof item.detail === 'string') {
              item.detail = JSON.parse(item.detail);
            }
          });

          this.orderList = orderList;
        } else {
          // alert(data.msg);
        }
      });
    },
      }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style lang="less" scoped>
  @import '../assets/less/tob-index.less';
  @import '../assets/less/yuyue.less';
  .container {
    background: #f3f3f3;
  }
</style>
