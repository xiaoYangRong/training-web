<template>
  <div>
    <Menu :menuItem="menuItem"></Menu>
    <div class="main">
      <div class="el-header">
          <span style="font-size: 16px">
            <nuxt-link to="/home/home">
              <el-button type="text">首页</el-button>
            </nuxt-link>
            <span>/</span>
            <nuxt-link to="/info/bookInfo">
              <el-button type="text">{{book.name}}</el-button>
            </nuxt-link>
          </span>
      </div>
      <div class="aside">
        <img :src="book.picture" style="width: 250px;height: 300px"/>
      </div>
      <el-main>
        <div class="box" style="padding-bottom: 30px">
          <h2>{{book.name}}</h2>
        </div>
        <div class="box">
          <span>型号</span><span style="padding-left: 80px">{{book.number}}</span>
          <h2></h2>
          <span>库存状态</span><span style="padding-left: 50px">{{book.count}}</span>
        </div>
        <div class="box">
          <span style="font-size: 36px">￥{{book.price}}</span>
        </div>
        <div class="box">
          <el-input-number size="small" v-model="buyNumber" :min="1"></el-input-number>
          <el-button type="danger" round size="small" @click="$addShopping">加入购物车</el-button>
          <h1 style="padding-top: 10px "></h1>
          <span><i class="el-icon-star-off"></i>
              <el-button type="text" class="collect_btn">收藏</el-button>
              <el-rate v-model="value" disabled show-score text-color="#ff9900" score-template="{value}"></el-rate>
            </span>
        </div>
      </el-main>
      <div class="border">
        <el-tabs v-model="activeName" @tab-click="handleClick">
          <el-tab-pane label="商品描述" name="first">
            <div class="boxx">
              <h3 style="color: #ff2953">精彩书摘</h3>
            </div>
            <div>
              <div style="padding-top: 20px">
                <label>
                  {{book.describe}}
                </label>
              </div>
            </div>
          </el-tab-pane>
          <el-tab-pane label="商品评价" name="second">
            <div>
              <div v-if="commentStatus">
                <div v-for="(item,index) in apprice" :key="index">
                  <el-card class="box-card comment shape ">
                    <img :src=" head" height="60" width="60"/>
                    <span class="user-name">{{item.suser.username}}</span>
                    <p class="user-comment">{{item.content}}</p>
                  </el-card>
                </div>
              </div>
              <div class="no-data" v-else>
                <i class="el-icon-warning"></i>目前暂无任何评价
              </div>
              <h4 style="padding-top: 30px">如果您对本商品有什么问题或经验，请在此留下您的意见和建议！</h4>
              <el-form ref="form" :model="form" label-width="100px">
                <el-form-item label="您的姓名：" prop="name">
                  <el-rate v-model="form.start"></el-rate>
                </el-form-item>
                <el-form-item label="您的评价：">
                  <el-input type="textarea" v-model="form.comment"></el-input>
                </el-form-item>
                <el-form-item>
                  <el-button type="primary" @click="submitForm">立即提交</el-button>
                  <el-button @click="resetForm">重置</el-button>
                </el-form-item>
              </el-form>
            </div>
          </el-tab-pane>
        </el-tabs>
      </div>
    </div>
    <Footer></Footer>
  </div>
</template>

<script>
  import Menu from '../../components/menu/menu'
  import * as BookRequest from '../../assets/info/bookInfo'
  import Footer from '../../components/footer/footer'
  export default {
    components: {
      Menu,
      Footer
    },
    data(){
      return {
        menuItem: 'index',
        id: 0,
        book: {},
        apprice:[],
        bookName: '追风筝的人',
        productImg: '../img/product1.png',
        bookNumber: '111100',
        bookCount: '100',
        bookPrice: '999.00',
        buyNumber: '1',
        value: 3.7,
        activeName: 'first',
        bookSummary: '12岁的阿富汗富家少爷阿米尔与仆人哈桑情同手足。然而，在一场风筝比赛后，发生了一件悲惨不堪的' +
        '事，阿米尔为自己的懦弱感到自责和痛苦，逼走了哈桑，不久，自己也跟随父亲逃往美国。成年后的阿米尔始终无法原' +
        '谅自己当年对哈桑的背叛。为了赎罪，阿米尔再度踏上暌违二十多年的故乡，希望能为不幸的好友尽最后一点心力，却' +
        '发现一个惊天谎言，儿时的噩梦再度重演，阿米尔该如何抉择？故事如此残忍而又美丽，作者以温暖细腻的笔法' +
        '勾勒人性的本质与救赎，读来令人荡气回肠。',
        commentStatus: true,
        head:'../img/head1.png',
        userName: '冯世杰',
        userComment: 'jsdjfkjefewjkns人家可能那就是发表哦i看你发的是iOK美式咖啡' +
        '电脑发快递师傅你就开始地方搞大锅饭大概多少广泛大锅饭大锅饭大概',
        form: {
          star: 0,
          comment: ''
        }
      }
    },
    methods: {
      handleClick(tab, event) {
        console.log(tab, event);
      },
      submitForm() {
        BookRequest.addBookApprice(this)
      },
      resetForm() {
        this.form.star = 0
        this.form.comment = ''
      },
      $addShopping(){
        BookRequest.addShoppingList(this)
      }
    },
    created(){
      if(this.$route.query.id !== null){
        this.id = this.$route.query.id
        console.log(this.$route.query.id)
        BookRequest.getBookInfo(this)
      }

    }

  }
</script>

<style>
  .main {
    margin: 0 150px;
    padding: 50px;
    min-height: 580px;
  }

  .el-header {
    padding: 0 20px;
    box-sizing: border-box;
  }

  .aside {
    margin-top: 10px;
    margin-bottom: 10px;
    width: 500px;
  }

  .box {
    box-shadow: 0 1px 0 #cbc6c6;
    width: 500px;
    padding: 20px 0;
  }

  .boxx {
    box-shadow: 0 1px 0 #cbc6c6;
    padding: 20px 0;
  }

  .collect_btn {
    font-size: 16px;
    color: #47494e;
  }

  .no-data {
    padding: 50px 0;
    background: rgba(28, 31, 33, .05);
    font-size: 16px;
    color: #9199A1;
    text-align: center;
  }

  .border {
    border: 1px solid #dad5d5;
    padding: 20px;
  }

  .shape {
    box-shadow: 0 8px 16px 0 rgba(7, 17, 27, .1);
    border-radius: 12px;
    margin-bottom: 8px;
    padding: 8px 10px 10px;
  }

  img {
    float: left;
  }

  .user-name {
    display: block;
    margin-top: 5px;
    font-size: 18px;
    line-height: 35px;
    margin-left: 25px;
    font-weight: 700;
  }

  .user-comment {
    font-size: 14px;
  }
  .el-card__body {
    display: block;
  }
</style>
