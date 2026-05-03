<template>
  <div class="login">
    <div class="login-box">
      <div class="login-visual">
        <img src="@/assets/img_denglu_bj.jpg" alt="" />
        <div class="ink-wash-mask" />
        <div class="visual-content">
          <h1>忆流年</h1>
          <p class="cn-subtitle">以古意入味，以系统提效</p>
          <p class="en-subtitle">Timeless Gastronomy Admin</p>
          <span class="seal">外卖</span>
        </div>
      </div>
      <div class="login-form">
        <el-form ref="loginForm" :model="loginForm" :rules="loginRules">
          <div class="login-form-title">
            <img
              src="@/assets/login/icon_logo.png"
              style="width: 180px; height: auto"
              alt=""
            />
            <div class="sys-title">
              <h2>忆流年外卖</h2>
              <p>餐饮管理后台</p>
            </div>
          </div>
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
      </div>
    </div>
    <p class="copyright">© 2026 忆流年外卖 · Timeless Gastronomy</p>
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
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  height: 100%;
  position: relative;
  overflow: hidden;
  background:
    radial-gradient(circle at 20% 20%, rgba(214, 181, 109, 0.18), transparent 32%),
    radial-gradient(circle at 80% 75%, rgba(31, 47, 44, 0.10), transparent 35%),
    linear-gradient(135deg, #fbf7ef 0%, #f5efe2 46%, #efe3cf 100%);
}

.login::before,
.login::after {
  content: '';
  position: absolute;
  pointer-events: none;
}

.login::before {
  right: -80px;
  top: -60px;
  width: 420px;
  height: 300px;
  border-radius: 50%;
  background:
    radial-gradient(circle at 40% 45%, rgba(31, 47, 44, 0.10) 0%, rgba(31, 47, 44, 0.02) 42%, rgba(31, 47, 44, 0) 72%),
    radial-gradient(circle at 62% 58%, rgba(255, 255, 255, 0.20) 0%, rgba(255, 255, 255, 0) 66%);
  filter: blur(2px);
}

.login::after {
  left: -120px;
  bottom: -120px;
  width: 520px;
  height: 360px;
  border-radius: 50%;
  background:
    radial-gradient(circle at 56% 52%, rgba(121, 110, 89, 0.16) 0%, rgba(121, 110, 89, 0.06) 38%, rgba(121, 110, 89, 0) 68%),
    radial-gradient(circle at 62% 58%, rgba(214, 181, 109, 0.14) 0%, rgba(214, 181, 109, 0) 62%);
  filter: blur(1px);
}

.login-box {
  width: 1040px;
  height: 560px;
  border-radius: 24px;
  display: flex;
  background: rgba(255, 252, 246, 0.94);
  border: 1px solid #e6d8c1;
  box-shadow: 0 20px 50px rgba(63, 52, 37, 0.16);
  overflow: hidden;
}

.login-visual {
  width: 56%;
  height: 100%;
  position: relative;

  img {
    width: 100%;
    height: 100%;
    object-fit: cover;
    border-radius: 24px 0 0 24px;
    filter: saturate(55%) brightness(1.01);
  }
}

.ink-wash-mask {
  position: absolute;
  inset: 0;
  background:
    radial-gradient(circle at 18% 25%, rgba(18, 30, 43, 0.3) 0%, rgba(18, 30, 43, 0.05) 48%, rgba(18, 30, 43, 0) 72%),
    radial-gradient(circle at 72% 18%, rgba(216, 169, 58, 0.22) 0%, rgba(216, 169, 58, 0) 40%),
    linear-gradient(120deg, rgba(31, 47, 44, 0.46) 0%, rgba(31, 47, 44, 0.16) 56%, rgba(251, 248, 241, 0.16) 100%);
}

.visual-content {
  position: absolute;
  left: 56px;
  bottom: 64px;
  color: #f8f0df;

  h1 {
    font-size: 58px;
    letter-spacing: 8px;
    margin: 0 0 8px;
    font-weight: 600;
  }

  .cn-subtitle {
    font-size: 17px;
    margin: 0 0 12px;
  }

  .en-subtitle {
    font-size: 13px;
    letter-spacing: 1px;
    color: #f4e4c4;
  }

  .seal {
    display: inline-block;
    margin-top: 22px;
    border: 1px solid rgba(216, 169, 58, 0.85);
    color: #efdbb2;
    padding: 6px 10px;
    border-radius: 4px;
    font-size: 13px;
    letter-spacing: 2px;
  }
}

.title {
  margin: 0px auto 10px auto;
  text-align: left;
  color: #707070;
}

.login-form {
  background: linear-gradient(180deg, rgba(255, 252, 246, 0.9) 0%, rgba(249, 242, 228, 0.9) 100%);
  width: 44%;
  border-radius: 0 24px 24px 0;
  display: flex;
  justify-content: center;
  align-items: center;
  padding: 0 22px;
  box-shadow: inset 0 0 0 1px #e8dcc8;
  .el-form {
    width: 292px;
    height: 360px;
  }
  .el-form-item {
    margin-bottom: 26px;
  }
  .el-form-item.is-error .el-input__inner {
    border: 1px solid #d7746b !important;
    background: #fffdf8 !important;
  }
  .input-icon {
    height: 32px;
    width: 18px;
    margin-left: -2px;
  }
  .el-input__inner {
    border: 1px solid #d7cbb9;
    border-radius: 14px;
    background: rgba(255, 250, 240, 0.92);
    font-size: 13px;
    font-weight: 400;
    color: #000000;
    height: 46px;
    line-height: 46px;
  }
  .el-input__inner:focus {
    border-color: #c99a2e;
    box-shadow: 0 0 0 2px rgba(201, 154, 46, 0.15);
  }
  .el-input__prefix {
    left: 14px;
    display: flex;
    align-items: center;
    color: #9b7a3d;
    opacity: 0.92;
  }
  .el-input--prefix .el-input__inner {
    padding-left: 40px;
  }
  .el-input__inner::placeholder {
    color: #b8a98a;
  }
  .el-form-item--medium .el-form-item__content {
    line-height: 46px;
  }
  .el-input--medium .el-input__icon {
    line-height: 46px;
    font-size: 17px;
    color: #9b7a3d;
    opacity: 0.92;
  }
  .el-input.is-focus .el-input__icon,
  .el-input__inner:focus + .el-input__prefix .el-input__icon {
    color: #8a6428;
    opacity: 1;
  }
}

.login-btn {
  border-radius: 22px;
  padding: 12px 20px !important;
  margin-top: 14px;
  font-weight: 500;
  font-size: 12px;
  border: 1px solid #c99a2e;
  color: #3b2d13;
  background: linear-gradient(90deg, #c89b3c 0%, #d8b75f 100%);
  height: 46px;
  &:hover,
  &:focus {
    background: linear-gradient(90deg, #b8892f 0%, #cca94f 100%);
    border-color: #c89b3c;
    color: #3b2d13;
    transform: translateY(-1px);
    box-shadow: 0 8px 16px rgba(166, 124, 34, 0.2);
  }
}
.login-form-title {
  height: 124px;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  margin-bottom: 24px;
  .sys-title {
    margin-top: 8px;
    text-align: center;
    h2 {
      margin: 0;
      color: #1f2a35;
      font-size: 22px;
      font-weight: 700;
      letter-spacing: 1px;
    }
    p {
      margin: 6px 0 0;
      color: #7a6a55;
      font-size: 13px;
    }
  }
  .title-label {
    font-weight: 500;
    font-size: 20px;
    color: #ffffff;
    margin-left: 10px;
  }
}

.copyright {
  margin-top: 18px;
  color: #8f816d;
  font-size: 12px;
  letter-spacing: 0.5px;
}
</style>
