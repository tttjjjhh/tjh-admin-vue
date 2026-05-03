<template>
  <div class="login">
    <div class="login-box">
      <img src="@/assets/login/login-l.png" alt="" />
      <div class="login-form">
        <el-form ref="loginForm" :model="loginForm" :rules="loginRules">
          <div class="login-form-title">
            <img
              src="@/assets/login/icon_logo.png"
              style="width: 180px; height: auto"
              alt=""
            />
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
  justify-content: center;
  align-items: center;
  height: 100%;
  background:
    radial-gradient(circle at 16% 18%, rgba(201, 154, 46, 0.22) 0%, rgba(201, 154, 46, 0) 28%),
    radial-gradient(circle at 82% 14%, rgba(255, 248, 232, 0.22) 0%, rgba(255, 248, 232, 0) 26%),
    linear-gradient(120deg, #17324d 0%, #0f2238 42%, #f7f1e6 42%, #fbf8f1 100%);
}

.login-box {
  width: 1000px;
  height: 474.38px;
  border-radius: 14px;
  display: flex;
  box-shadow: 0 18px 46px rgba(15, 34, 56, 0.32);
  overflow: hidden;
  img {
    width: 60%;
    height: 100%;
    object-fit: cover;
    border-radius: 14px 0 0 14px;
    box-shadow: inset -10px 0 30px rgba(15, 34, 56, 0.12);
  }
}

.title {
  margin: 0px auto 10px auto;
  text-align: left;
  color: #707070;
}

.login-form {
  background: linear-gradient(180deg, #fffdf8 0%, #fcf7ee 100%);
  width: 40%;
  border-radius: 0 14px 14px 0;
  display: flex;
  justify-content: center;
  align-items: center;
  padding: 0 14px;
  box-shadow: inset 0 0 0 1px #e8dcc8;
  .el-form {
    width: 214px;
    height: 307px;
  }
  .el-form-item {
    margin-bottom: 30px;
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
    border-radius: 8px;
    background: #fffaf2;
    font-size: 13px;
    font-weight: 400;
    color: #000000;
    height: 32px;
    line-height: 32px;
  }
  .el-input__inner:focus {
    border-color: #c99a2e;
    box-shadow: 0 0 0 2px rgba(201, 154, 46, 0.15);
  }
  .el-input__prefix {
    left: 0;
  }
  .el-input--prefix .el-input__inner {
    padding-left: 26px;
  }
  .el-input__inner::placeholder {
    color: #aeb5c4;
  }
  .el-form-item--medium .el-form-item__content {
    line-height: 32px;
  }
  .el-input--medium .el-input__icon {
    line-height: 32px;
  }
}

.login-btn {
  border-radius: 22px;
  padding: 11px 20px !important;
  margin-top: 10px;
  font-weight: 500;
  font-size: 12px;
  border: 1px solid #c99a2e;
  color: #3b2d13;
  background: linear-gradient(90deg, #d8a93a 0%, #c99a2e 100%);
  &:hover,
  &:focus {
    background: linear-gradient(90deg, #e1b44a 0%, #d3a33c 100%);
    border-color: #d3a33c;
    color: #3b2d13;
  }
}
.login-form-title {
  height: 70px;
  display: flex;
  justify-content: center;
  align-items: center;
  margin-bottom: 40px;
  .title-label {
    font-weight: 500;
    font-size: 20px;
    color: #ffffff;
    margin-left: 10px;
  }
}
</style>
