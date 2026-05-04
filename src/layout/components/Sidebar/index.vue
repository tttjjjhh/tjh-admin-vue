<template>
  <div>
    <div class="logo">
      <!-- <img
        src="./../../../assets/logo.png"
        width="122.5"
        alt=""
      > -->
      <!-- <img
        src="@/assets/login/login-logo.png"
        alt=""
        style="width: 120px; height: 31px"
      /> -->
      <div v-if="!isCollapse" class="sidebar-logo">
        <img src="@/assets/login/yiliunian-logo.svg" alt="忆流年外卖 logo" />
      </div>
      <div v-else
           class="sidebar-logo-mini">
        <img src="@/assets/login/mini-logo.png" alt="忆流年" />
      </div>
    </div>
    <el-scrollbar wrap-class="scrollbar-wrapper">
      <el-menu :default-openeds="defOpen"
               :default-active="defAct"
               :collapse="isCollapse"
               :background-color="variables.menuBg"
               :text-color="variables.menuText"
               :active-text-color="variables.menuActiveText"
               :unique-opened="false"
               :collapse-transition="false"
               mode="vertical">
        <sidebar-item v-for="route in routes"
                      :key="route.path"
                      :item="route"
                      :base-path="route.path"
                      :is-collapse="isCollapse" />
        <!-- <div class="sub-menu">
          <div class="avatarName">
            {{ name }}
          </div>
          <div class="img">
            <img
              src="./../../../assets/icons/btn_close@2x.png"
              class="outLogin"
              alt="退出"
              @click="logout"
            />
          </div>
        </div> -->
      </el-menu>
    </el-scrollbar>
  </div>
</template>

<script lang="ts">
import { Component, Prop, Vue } from 'vue-property-decorator'
import { AppModule } from '@/store/modules/app'
import { UserModule } from '@/store/modules/user'
import SidebarItem from './SidebarItem.vue'
import variables from '@/styles/_variables.scss'
import { getSidebarStatus, setSidebarStatus } from '@/utils/cookies'
import Cookies from 'js-cookie'
@Component({
  name: 'SideBar',
  components: {
    SidebarItem
  }
})
export default class extends Vue {
  private restKey: number = 0
  get name() {
    return (UserModule.userInfo as any).name
      ? (UserModule.userInfo as any).name
      : JSON.parse(Cookies.get('user_info') as any).name
  }
  get defOpen() {
    // const urlArr = this.$route.path.split('/')
    // const openStr = urlArr.length > 2 ? `/${urlArr[1]}` : '/'
    let path = ['/']
    this.routes.forEach((n: any, i: number) => {
      if (n.meta.roles && n.meta.roles[0] === this.roles[0]) {
        path.splice(0, 1, n.path)
      }
    })
    return path
  }

  get defAct() {
    let path = this.$route.path
    return path
  }

  get sidebar() {
    return AppModule.sidebar
  }

  get roles() {
    return UserModule.roles
  }

  get routes() {
    let routes = JSON.parse(
      JSON.stringify([...(this.$router as any).options.routes])
    )
    console.log('-=-=routes=-=-=', routes)
    console.log('-=-=routes=-=-=', this.roles[0])
    let menuList = []
    let menu = routes.find(item => item.path === '/')
    if (menu) {
      menuList = menu.children
    }
    console.log('-=-=routes=-wwww=-=', routes)
    return menuList
  }

  get variables() {
    return variables
  }

  get isCollapse() {
    return !this.sidebar.opened
  }
  private async logout() {
    this.$store.dispatch('LogOut').then(() => {
      // location.href = '/'
      this.$router.replace({ path: '/login' })
    })
    // this.$router.push(`/login?redirect=${this.$route.fullPath}`)
  }
}
</script>

<style lang="scss" scoped>
.logo {
  text-align: center;
  background: rgba(255,255,255,0.35);
  border: 1px solid rgba(165,130,80,0.18);
  border-radius: 8px;
  padding: 15px 0 0;
  height: 60px;
  border-bottom: 1px solid rgba(165,130,80,0.18);
  img {
    display: inline-block;
  }
}
.logo::after{content:'';position:absolute;right:8px;bottom:5px;width:56px;height:56px;border-radius:50%;border:1px solid rgba(154,116,62,.35);opacity:.25}
.sidebar-logo img{width:170px;height:38px;object-fit:contain;display:block;filter: drop-shadow(0 1px 1px rgba(72,56,35,.2));}
.brand-slogan{margin:1px 0 0 4px;font-size:10px;letter-spacing:1.4px;color:rgba(96,74,49,.78)}
.sidebar-logo-mini {
  img { width: 30px; height: 30px; display: block; margin: 0 auto; }
}
.el-scrollbar {
  height: 100%;
  position: relative;
  background: #E8D8BC;
  border-right: 1px solid rgba(165,130,80,0.18);
}
.el-scrollbar::before{content:'';position:absolute;left:-20px;bottom:70px;width:150px;height:150px;background:radial-gradient(circle,rgba(56,52,45,.10),rgba(56,52,45,0) 70%);opacity:.12;pointer-events:none}
.el-scrollbar::after{content:'忆';position:absolute;left:20px;bottom:16px;color:rgba(185,139,66,.35);font-size:24px;font-family:'STKaiti','KaiTi',serif;pointer-events:none}

.el-menu {
  border: none;
  height: calc(95vh - 40px);
  width: 100% !important;
  padding: 47px 15px 0;
}
</style>
