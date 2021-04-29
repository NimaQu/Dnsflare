<template>
    <el-card class="box-card">
        <div
            slot="header"
            class="clearfix"
        >
            <span>登录</span>
        </div>
        <el-form
            ref="form"
            :model="model"
            label-width="120px"
        >
            <el-form-item
                label="Token"
                prop="token"
            >
                <el-input v-model="model.token" />
            </el-form-item>
            <el-divider>或者</el-divider>
            <el-form-item
                label="邮箱地址"
                prop="email"
            >
                <el-input v-model="model.email" />
            </el-form-item>
            <el-form-item
                label="全局 API Key"
                prop="globalToken"
            >
                <el-input v-model="model.globalToken" />
            </el-form-item>

            <el-form-item label="记住凭证">
                <el-switch v-model="model.save" />
            </el-form-item>
            <el-form-item>
                <el-button
                    type="primary"
                    :loading="isLoading"
                    @click="submit"
                >
                    提交
                </el-button>
                <el-button @click="reset">
                    重置
                </el-button>
            </el-form-item>
        </el-form>

    <el-collapse v-model="help" accordion>
      <el-collapse-item title="使用教程" name="1">
        <el-tabs v-model="help" @tab-click="handleClick">
          <el-tab-pane>
            <span slot="label">API Token 登录</span>
            <ol>
              <li>
                打开<a href="https://dash.cloudflare.com/profile/api-tokens"
                  >Cloudflare 的 API Token 设定</a
                >
              </li>
              <li>
                在 API Tokens 一栏中选择 Create Token, 拉到最下面选择 Create
                Custom Token，给予以下权限
              </li>
              <ul>
                <li>Zone.DNS 写权限 (用于写入 DNS 记录)</li>
                <li>Zone.Zone 读权限 (用于读取域名列表)</li>
              </ul>
              <li>填入到 Token 一栏中点击登录</li>
            </ol>
            <img src="/token.png">
          </el-tab-pane>
          <el-tab-pane>
            <span slot="label">Global API Key 登录</span>
            <ol>
              <li>
                打开<a href="https://dash.cloudflare.com/profile/api-tokens"
                  >Cloudflare 的 API Token 设定</a
                >
              </li>
              <li>
                在 API Keys 一栏中点击查看 Global API Key，复制后填入全局 API Key
                一栏中，邮箱为 Global API Key 对应邮箱地址
              </li>
            </ol>
            <img src="/global_api.png">
          </el-tab-pane>
        </el-tabs>
      </el-collapse-item>
    </el-collapse>

    </el-card>
</template>

<script lang="ts">
import { Vue, Component } from 'vue-property-decorator'
import { userTokenVerify, userEmailVerify } from '../api/account'
import { UserModule } from '../store/module'

@Component({})
export default class LoginRoute extends Vue {
    private model = {
        token: '',
        save: false,
        email: '',
        globalToken: '',
    }

    private isLoading = false

    get tokenStatus(): boolean {
        // Cloudflare token should have 40
        if (this.model.token.length === 40) {
            return true
        }

        return false
    }

    mounted() {
        // Clean token
        if (!UserModule.saveToken) {
            UserModule.logout()
        }

        this.model.token = UserModule.token
        this.model.save = UserModule.saveToken
        this.model.email = UserModule.email
        this.model.globalToken = UserModule.globalToken

        if (this.model.save) {
            this.submit()
        }
    }

    onLoginSuccessful() {
        this.$message('登录成功, 即将跳转管理页面')

        setTimeout(() => {
            this.$router.push({ name: 'ZoneList' })
        }, 1000)
    }

    async submit() {
        this.isLoading = true
        if (this.tokenStatus) {
            const status = await userTokenVerify(this.model.token)

            if (status) {
                UserModule.loginByToken(this.model)
                this.onLoginSuccessful()
            } else {
                this.$message({
                    type: 'error',
                    message: 'Token 错误',
                })
            }
        } else {
            const status = await userEmailVerify(this.model.email, this.model.globalToken)

            if (status) {
                UserModule.loginByEmail(this.model)
                this.onLoginSuccessful()
            } else {
                this.$message({
                    type: 'error',
                    message: '邮箱或者全局 Key 错误',
                })
            }
        }

        this.isLoading = false
    }

    reset() {
        this.model.token = ''
        this.model.save = false
    }
}
</script>

<style lang="scss" scoped>
    .login-panel {
        margin: auto;
        margin-top: 20px;
        max-width: 400px;
    }
</style>
