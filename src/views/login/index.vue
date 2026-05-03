<template>
  <div class="login">
    <div class="mist-overlay" />
    <div class="login-shell">
      <section class="login-visual">
        <div class="visual-mask" />
        <div class="visual-brand">
          <img src="@/assets/login/yiliunian-logo.svg" alt="忆流年外卖 logo" />
          <p>山水入馔 · 岁月留香</p>
        </div>
        <div class="visual-seal">忆流年</div>
      </section>

      <section class="login-card">
        <div class="login-form-title">
          <img src="@/assets/login/yiliunian-logo.svg" alt="忆流年外卖 logo" />
          <p>TIMELESS GASTRONOMY</p>
        </div>

        <el-form ref="loginForm" :model="loginForm" :rules="loginRules">
          <el-form-item prop="username">
            <el-input
              v-model="loginForm.username"
              type="text"
              auto-complete="off"
              placeholder="账号"
              prefix-icon="iconfont icon-user"
            />
          </el-form-item>
          <el-form-item prop="password">
            <el-input
              v-model="loginForm.password"
              type="password"
              placeholder="密码"
              prefix-icon="iconfont icon-lock"
              @keyup.enter.native="handleLogin"
            />
          </el-form-item>
          <el-form-item style="width: 100%">
            <el-button
              :loading="loading"
              class="login-btn"
              size="medium"
              type="primary"
              style="width: 100%"
              @click.native.prevent="handleLogin"
            >
              <span v-if="!loading">登录</span>
              <span v-else>登录中...</span>
            </el-button>
          </el-form-item>
        </el-form>
      </section>
    </div>
  </div>
</template>

<script lang="ts">
import { Component, Vue, Watch } from 'vue-property-decorator'
import { Route } from 'vue-router'
import { Form as ElForm, Input } from 'element-ui'
import { UserModule } from '@/store/modules/user'
import { isValidUsername } from '@/utils/validate'

@Component({
  name: 'Login',
})
export default class extends Vue {
  private validateUsername = (rule: any, value: string, callback: Function) => {
    if (!value) {
      callback(new Error('请输入用户名'))
    } else {
      callback()
    }
  }
  private validatePassword = (rule: any, value: string, callback: Function) => {
    if (value.length < 6) {
      callback(new Error('密码必须在6位以上'))
    } else {
      callback()
    }
  }
  private loginForm = {
    username: 'admin',
    password: '123456',
  } as {
    username: String
    password: String
  }

  loginRules = {
    username: [{ validator: this.validateUsername, trigger: 'blur' }],
    password: [{ validator: this.validatePassword, trigger: 'blur' }],
  }
  private loading = false
  private redirect?: string

  @Watch('$route', { immediate: true })
  private onRouteChange(route: Route) {}

  // 登录
  private handleLogin() {
    ;(this.$refs.loginForm as ElForm).validate(async (valid: boolean) => {
      if (valid) {
        this.loading = true
        await UserModule.Login(this.loginForm as any)
          .then((res: any) => {
            if (String(res.code) === '1') {
              this.$router.push('/')
            } else {
              // this.$message.error(res.msg)
              this.loading = false
            }
          })
          .catch(() => {
            // this.$message.error('用户名或密码错误！')
            this.loading = false
          })
      } else {
        return false
      }
    })
  }
}
</script>

<style lang="scss">
.login {
  position: relative;
  display: flex;
  justify-content: center;
  align-items: center;
  height: 100%;
  overflow: hidden;
  background:
    radial-gradient(circle at 18% 82%, rgba(47, 72, 88, 0.24), rgba(47, 72, 88, 0) 34%),
    radial-gradient(circle at 82% 18%, rgba(176, 138, 77, 0.22), rgba(176, 138, 77, 0) 32%),
    linear-gradient(180deg, #f8f5ef 0%, #f2eee5 100%);
}

.login::before {
  content: '';
  position: absolute;
  inset: 0;
  pointer-events: none;
  background: radial-gradient(rgba(0, 0, 0, 0.03) 0.9px, transparent 0.9px);
  background-size: 4px 4px;
  opacity: 0.35;
}

.mist-overlay {
  position: absolute;
  left: 12%;
  right: 12%;
  bottom: 12%;
  height: 160px;
  pointer-events: none;
  background: linear-gradient(180deg, rgba(93, 106, 118, 0.2), rgba(93, 106, 118, 0));
  clip-path: polygon(0 78%, 10% 58%, 22% 75%, 37% 48%, 49% 66%, 61% 45%, 75% 60%, 88% 42%, 100% 58%, 100% 100%, 0 100%);
  opacity: 0.22;
}

.login-shell {
  position: relative;
  z-index: 1;
  width: 1120px;
  min-height: 580px;
  border-radius: 18px;
  overflow: hidden;
  display: grid;
  grid-template-columns: 58% 42%;
  box-shadow: 0 20px 50px rgba(25, 30, 37, 0.15);
  border: 1px solid #e2dbcd;
  background: rgba(255, 255, 255, 0.72);
}

.login-visual {
  position: relative;
  background:
    radial-gradient(circle at 15% 85%, rgba(47, 72, 88, 0.34), rgba(47, 72, 88, 0) 32%),
    linear-gradient(180deg, #233847 0%, #1d2f3d 60%, #1b2733 100%);
  display: flex;
  align-items: flex-start;
  justify-content: center;
  padding: 56px 46px;
}

.visual-mask {
  position: absolute;
  inset: 0;
  background:
    radial-gradient(circle at 30% 18%, rgba(255, 255, 255, 0.16), rgba(255, 255, 255, 0) 40%),
    linear-gradient(180deg, rgba(255, 255, 255, 0.06), rgba(255, 255, 255, 0));
}

.visual-brand {
  position: relative;
  z-index: 2;
  text-align: left;
  color: rgba(244, 235, 222, 0.88);

  img {
    width: 340px;
    max-width: 100%;
    filter: drop-shadow(0 3px 10px rgba(0, 0, 0, 0.4));
  }

  p {
    margin-top: 14px;
    font-size: 14px;
    letter-spacing: 3px;
  }
}

.visual-seal {
  position: absolute;
  right: 38px;
  bottom: 38px;
  writing-mode: vertical-rl;
  color: rgba(213, 54, 54, 0.7);
  border: 1px solid rgba(213, 54, 54, 0.55);
  border-radius: 8px;
  padding: 8px 5px;
  font-family: 'STKaiti', 'KaiTi', serif;
  letter-spacing: 2px;
}

.login-card {
  background: rgba(250, 247, 240, 0.95);
  padding: 54px 52px;
  display: flex;
  flex-direction: column;
  justify-content: center;
  border-left: 1px solid rgba(176, 138, 77, 0.2);
}

.login-form-title {
  display: flex;
  flex-direction: column;
  align-items: flex-start;
  margin-bottom: 36px;

  img {
    width: 250px;
    max-width: 100%;
  }

  p {
    margin-top: 8px;
    color: #7f8b98;
    font-size: 12px;
    letter-spacing: 2px;
  }
}

.login-form {
  .el-form-item {
    margin-bottom: 22px;
  }
}

.el-input__inner {
  border: 1px solid #d8d0c2;
  border-radius: 10px;
  height: 42px;
  line-height: 42px;
  color: #222b35;
  background: rgba(255, 255, 255, 0.9);
}

.el-input__inner:focus {
  border-color: #2f4858;
  box-shadow: 0 0 0 2px rgba(47, 72, 88, 0.13);
}

.el-input__prefix {
  left: 8px;
}

.el-input--prefix .el-input__inner {
  padding-left: 34px;
}

.login-btn {
  margin-top: 12px;
  border: 0;
  height: 42px;
  border-radius: 24px;
  font-size: 14px;
  letter-spacing: 2px;
  color: #fff;
  background: linear-gradient(90deg, #2f4858 0%, #3c5d71 100%);

  &:hover,
  &:focus {
    background: linear-gradient(90deg, #3c5d71 0%, #4d738d 100%);
    color: #fff;
  }
}
</style>
