<style lang="less">
  @import "./menu.less";
</style>
<template>
  <div class="menu">
    <div class="menu-content">
      <no-ssr>
        <el-row class="menu-row1">
          <el-col :span="12" v-if="loginFlag">
            <span>尊敬的用户您好！</span>
            <nuxt-link to="../../login/login">[登录]</nuxt-link>
            <nuxt-link to="../../register/register">[注册]</nuxt-link>
          </el-col>
          <el-col :span="12" v-else>
            尊敬的{{username}}用户您好！
          </el-col>
          <el-col :span="12" class="top-menu-right" v-if="loginFlag">
            <el-dropdown @command="handleCommand">
          <span class="el-dropdown-link">
            会员中心<i class="el-icon-arrow-down el-icon--right"></i>
          </span>
              <el-dropdown-menu slot="dropdown">
                <el-dropdown-item command="d">会员登陆</el-dropdown-item>
                <el-dropdown-item command="e">会员注册</el-dropdown-item>
              </el-dropdown-menu>
            </el-dropdown>
            <nuxt-link to="../../order/collect">收藏 ({{wishList}})</nuxt-link>
            <nuxt-link to="../../order/shopping">购物车</nuxt-link>
            <nuxt-link to="../../my-order">结账</nuxt-link>
          </el-col>
          <el-col :span="12" class="top-menu-right" v-else>
            <el-dropdown @command="handleCommand">
          <span class="el-dropdown-link">
            会员中心<i class="el-icon-arrow-down el-icon--right"></i>
          </span>
              <el-dropdown-menu slot="dropdown" >
                <el-dropdown-item command="a">会员中心</el-dropdown-item>
                <el-dropdown-item command="b">我的订单</el-dropdown-item>
                <el-dropdown-item command="c">注销登录</el-dropdown-item>
              </el-dropdown-menu>
            </el-dropdown>
            <nuxt-link to="../../order/collect">收藏 ({{wishList}})</nuxt-link>
            <nuxt-link to="../../order/shopping">购物车</nuxt-link>
            <nuxt-link to="../../my-order">结账</nuxt-link>
          </el-col>
        </el-row>
        <el-row class="menu-row2">
          <el-col :span="12">
            <img src="http://theme.opencartdemo.cn/book-2102-cn/image/catalog/logo3.png" alt="">
          </el-col>
          <el-col :span="12" class="top-menu-right">
            <el-popover
              placement="bottom"
              width="300"
              trigger="hover">
              <div v-for="(item, index) in shopCardList" :key="index">
                <el-card :body-style="{ padding: '0px' }">
                  <img :src="item.bookId.picture" class="image" width="80" height="100" style="display: inline-block">
                  <div class="shop-card-right">
                    <div>
                      <div class="shop-card-name">{{item.bookId.name}}</div>
                      <span>x{{item.number}}</span>
                    </div>
                    <div>
                      <el-button type="text" class="button">￥{{item.bookId.price}}</el-button>
                    </div>
                  </div>
                </el-card>
              </div>
              <nuxt-link to="/order/shopping" slot="reference">{{Count}}-个商品 -￥{{shoppingTotal}}</nuxt-link>
            </el-popover>
          </el-col>
        </el-row>
      </no-ssr>
    </div>
    <div class="menu-list">
      <el-menu
        :default-active="activeIndex"
        class="el-menu-demo"
        mode="horizontal"
        @select="handleSelect"
        background-color="#545c64"
        text-color="#fff"
        active-text-color="#ffd04b">
        <el-menu-item index="index">首页</el-menu-item>
        <el-submenu :index="item.first" v-for="(item,index) in menuList" :key="index">
          <template slot="title">{{item.first}}</template>
          <el-menu-item :index="sec" v-for="(sec,index) in item.second" :key="index" v-if="item.second">{{sec}}
          </el-menu-item>
        </el-submenu>
      </el-menu>
      <div class="menu-query">
        <el-input v-model="query" placeholder="请输入内容" class="input-with-select menu-query-input">
          <el-button slot="append" icon="el-icon-search" class="menu-query-button" @click="$btn_search"></el-button>
        </el-input>
      </div>
    </div>
  </div>
</template>

<script>
  import Cookies from 'js-cookie'
  import * as MenuRequest from '../../assets/menu/menu'

  export default {
    props: {
      menuItem: {
        type: String,
        default: 'index',
      },
      TotalPrice: {
        type: Number,
        default: 0,
      },
      shoppingCount: {
        type: Number,
        default: 0,
      }
    },
    data() {
      return {
        loginFlag: true,
        shopCardList: [],
        menuList: [],
        wishList: 0,
        Count: 0,
        shoppingTotal: 0.00,
        activeIndex: 'index',
        query: '',
        username: '',
        id: 0,
        books:[]
      }
    },
    methods: {
      handleCommand(command) {
        switch (command) {
          case 'a':
            this.$router.push({
              name: 'Info-info'
            })
            break;
          case 'b':
            this.$router.push({
              name: 'my-order'
            })
            break;
          case 'c':
            Cookies.set("suserId", "", -1)
            Cookies.set("username", "", -1)
            Cookies.set("email", "", -1)
            this.loginFlag = true
            break;
          case 'd':
            this.$router.push({
              name: 'login-login'
            })
            break;
          case 'e':
            this.$router.push({
              name: 'register-register'
            })
            break;
        }
      },
      handleSelect(key, keyPath) {
        this.activeIndex = key
        if (key == 'index') {
          this.$router.push({
            name: key,
            params: {
              menuType: key,
            }
          })
        } else {
          this.$router.push({
            name: "cartoon-cartoon",
            params: {
              menuType: key,
              first: keyPath[0],
              all: keyPath
            }
          })
        }
        this.$emit('refresh', key);
      },
      $btn_search() {
        MenuRequest.selectBookByName(this)
        this.$router.push({
          path: '/home/home',
          query: {
            bookList: this.books
          }
        })
      }
    },
    created() {
      this.activeIndex = this.menuItem
      if (Cookies.get("suserId") == null || typeof(Cookies.get("suserId")) == undefined) {

      } else {
        this.loginFlag = false
        this.username = Cookies.get("email")
        if (Cookies.get("username") != 'null') {
          this.username = Cookies.get("username")
        }
        this.wishList = Cookies.get("wishList");
        MenuRequest.getShoppingCount(this, Cookies.get("suserId"))
      }
      MenuRequest.getMenus(this)

    },
    watch: {
      menuItem() {
        console.log(this.menuItem, 1111)
        this.activeIndex = this.menuItem;
      },
      TotalPrice(){
        console.log(this.TotalPrice, 1111)
        this.shoppingTotal = this.TotalPrice
      },
      shoppingCount() {
        console.log(this.shoppingCount, 1111)
        this.Count = this.shoppingCount
      }
    }
  }
</script>

