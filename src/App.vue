<template>
  <div id="app" class="clearfix">
    <div v-if="isLogin" class="app-content clearfix">
      <!-- 左侧menu信息 -->
      <div class="menu-wrap">
        <menu-header></menu-header>
        <menu-content></menu-content>
      </div>

      <!-- 导航提示 -->
      <el-breadcrumb class="router-tips" separator=">">
        <el-breadcrumb-item v-for="(b,index) in bread" :to="b.to" :key="index">{{b.name}}</el-breadcrumb-item>
      </el-breadcrumb>
      <!-- 头部信息 -->
      <div class="user">
        <i class="icon icon-single" @dblclick="fullScreen"></i>
        <el-dropdown @command="handleCommand">
          <span class="el-dropdown-link">
            {{username}}
            <i class="el-icon-arrow-down el-icon--right"></i>
          </span>
          <el-dropdown-menu slot="dropdown">
            <!-- <el-dropdown-item command='a'>个人中心</el-dropdown-item> -->
            <el-dropdown-item command='b'>修改密码</el-dropdown-item>
          </el-dropdown-menu>
        </el-dropdown>
      </div>
      <div class="line"></div>
      <div class="logout" @click="logOut">
        <i class="icon icon-logout"></i>
        <span>退出</span>
      </div>

      <!-- 内容区域 -->
      <div class="content-wrap">
        <router-view></router-view>
      </div>
    </div>
    <login @loginSuccess='loginSuccess' v-else></login>

    <!-- 版权申请区域 -->
    <app-footer></app-footer>
  </div>
</template>

<script>
import menuHeader from "@/components/common/header"
import menuContent from "@/components/common/menu"
import AppFooter from "@/components/common/AppFooter"
import login from "@/components/Login"
export default {
  name: "App",
  components: {
    menuHeader,
    menuContent,
    AppFooter,
    login
  },
  computed: {
    isLogin () {
      return this.$store.state.loginStatus
    }
  },
  data () {
    return {
      username: '',
      routerMap: {},
      systemname: '',
      bread: [{
        name: "首页",
        to: "/index"
      }]
    }
  },
  created () {
    if (this.util.getCookies("accesstoken")) {
      this.$store.commit('login')
    }
    this.systemname = this.$('systemname')
    window.document.title = this.systemname
    // this.username = this.$('username')
    let routes = this.$router.options.routes
    for (let i in routes) {
      let item = routes[i]
      let name = item.name
      if (name !== '') {
        if (!item.children) {
          let arr = []
          arr.push({
            name: name,
            to: item.path
          })
          this.routerMap[name] = arr
        } else {
          for (let chIndex in item.children) {
            name = item.name
            let chItem = item.children[chIndex]
            let arr = []
            arr.push({
              name: name,
              to: item.path
            })
            this.routerMap[name] = arr
            name = chItem.name
            let arr2 = arr.slice(0)
            arr2.push({
              name: name,
              to: chItem.path
            })
            this.routerMap[name] = arr2
          }
        }
      }
    }
    this.bread = this.routerMap[this.util.getCookies("bread") ? this.util.getCookies("bread") : '首页']
    document.addEventListener("visibilitychange", this.handleVisibilityChange, false)
  },
  methods: {
    async handleVisibilityChange () {
      let h = document.hidden
      if (!h) {
        window.document.title = this.systemname + " | " + this.util.getCookies("bread")
      } else {
        window.document.title = "(●´ω｀●)ゞ"
      }
    },
    loginSuccess (name) {
      this.$store.commit('login')
      this.username = name
    },
    fullScreen () {
      this.launchFullScreen(document.documentElement)
    },
    launchFullScreen (element) {
      if (element.requestFullscreen) {
        element.requestFullscreen()
      } else if (element.mozRequestFullScreen) {
        element.mozRequestFullScreen()
      } else if (element.webkitRequestFullscreen) {
        element.webkitRequestFullscreen()
      } else if (element.msRequestFullscreen) {
        element.msRequestFullscreen()
      }
    },
    logOut () {
      this.$store.commit('logout')
    },
    handleCommand (command) {
      if (command === 'a') {
        console.log(command)
      } else if (command === 'b') { // 修改密码
        console.log(command)
        this.$router.push({path: '/changePassword'})
      }
    }
  },
  watch: {
    '$route': {
      immediate: true,
      deep: true,
      handler (to, from) {
        const name = to.name
        this.bread = this.routerMap[name]
        if (this.bread) {
          let length = this.bread.length - 1
          this.util.setCookie('bread', this.bread[length].name)
          window.document.title = this.systemname + " | " + this.util.getCookies("bread")
        }
      }
    }
  }
}
</script>

<style lang="less" scoped>
#app {
  height: 100%;
  padding-bottom: 40px;
  overflow: hidden;
}
.app-content {
  position: relative;
  height: 100%;
}
.menu-wrap {
  width: 174px;
  height: 100%;
  overflow-y: hidden;
  background-color: #20a0ff;
  color: #fff;
  position: absolute;
}
.el-breadcrumb {
  position: absolute;
  left: 194px;
  right: 20px;
  top: 0;
  padding-left: 0px;
  height: 40px;
  line-height: 40px;
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
  border-bottom: 1px solid #aaa;
}
.content-wrap {
  position: relative;
  left: 174px;
  top: 40px;
  width: calc(~"100% - 174px");
  height: calc(~"100% - 40px");
  // overflow-y: scroll;
  overflow-x: hidden;
  // white-space: nowrap;
  min-width: 1500px;
}
.user {
  display: inline-block;
  height: 40px;
  position: absolute;
  right: 120px;
  span {
    line-height: 40px;
    color: #1296db;
  }
}
.line {
  position: absolute;
  height: 32px;
  top: 4px;
  right: 96px;
  border-left: 2px solid rgb(233, 237, 240);
}
.logout {
  position: absolute;
  height: 40px;
  right: 20px;
  display: inline-block;
  cursor: pointer;
  span {
    line-height: 40px;
    color: #1296db;
  }
}
</style>
