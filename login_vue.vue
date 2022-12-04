<template>
  <!-- 根节点 -->
  <div class="login-container">
    <div class="login-form-wrap">
      <!--
          el-form 表单组件
          每个表单项都必须使用 el-form-item 组件包裹
       -->

      <div class="login-head">
        <h1>登录</h1>
        <!-- <div class="logo"></div> -->
      </div>
      <!--
        配置 Form 表单验证：
        1、必须给 el-from 组件绑定 model 为表单数据对象
        2、给需要验证的表单项 el-form-Item 绑定 prop 属性
            注意prop属性需要制定表单对象中的数据名称
        3、通过 el-form 组件的 rule 属性配置验证规则

        手动触发表单验证：
        1. 给 el=from 设置 ref  （起个名字）
        2. 通过 ref 获取 el-form 组件，调用组件的 validate 方法进行验证
      -->

      <el-form
        class="login-form"
        ref="login-form"
        :model="user"
        :rules="formRules"
      >
        <!-- 提示文本 -->
        <el-form-item prop="mobile">
            <!-- 输入框 input 组件 双向绑定 from 数据 -->
            <el-input
              v-model="user.mobile"
              placeholder="请输入手机号"
            ></el-input>
        </el-form-item>
        <el-form-item prop="code">
            <!-- 输入框 input 组件 双向绑定 from 数据 -->
            <el-input
              v-model="user.code"
              placeholder="请输入验证码"
            ></el-input>
        </el-form-item>
          <el-form-item prop="agree">
            <el-checkbox v-model="user.agree">
              我已阅读并同意用户协议和隐私条款</el-checkbox>
          </el-form-item>
        <el-form-item>
            <el-button
              class="login-btn"
              type="primary"
              :loading="loginLoading"
              @click="onLogin"
            >登录</el-button>
        </el-form-item>
      </el-form>
    </div>
  </div>
</template>

<script>
import { login } from '@/api/user' // 按需加载

export default {
  name: 'LoginIndex',
  components: {},
  props: {},
  data () {
    return {
      user: {
        mobile: '13911111111', // 手机号
        code: '246810', // 验证码
        agree: false // 是否同意协议
      },
      // checked: false, // 是否同意协议的选中状态
      loginLoading: false, // 登录的 loading 状态
      formRules: { // 表单验证规则配置
        // 要验证的数据名称：规则列表[]
        mobile: [
          // 必填项
          { required: true, message: '请输入手机号', trigger: 'change' },
          { pattern: /^1[3|5|7|8|9]\d{9}$/, message: '请输入正确的手机号', trigger: 'change' }
        ],
        code: [
          { required: true, message: '请输入验证码', trigger: 'change' },
          { pattern: /^\d{6}$/, message: '请输入正确的验证码格式', trigger: 'change' }
        ],
        agree: [
          {
            // 自定义验证规则
            validator: (rule, value, callback) => {
              if (value) {
                // 验证通过
                callback()
              } else {
                // 验证失败
                callback(new Error('请勾选用户协议'))
              }
            },
            // message: '请勾选同意用户协议',
            trigger: 'change'
          }
        ]
      }
    }
  },
  computed: {},
  watch: {},
  created () {},
  mounted () {},
  methods: {
    onLogin () {
      // // 获取表单数据(根据接口要求绑定数据)
      // const user = this.user

      // 表单验证
      // validate 方法是异步的
      this.$refs['login-form'].validate(valid => {
        // 如果表单验证失败，停止请求提交
        if (!valid) {
          return
        }

        // 验证通过，提交登录
        this.login()
      })
    },

    login () {
      // 开启登陆中 loading...
      this.loginLoading = true

      login(this.user).then(res => {
        console.log(res) // 提取数据

        // 登录成功
        this.$message({
          message: '登录成功',
          type: 'success'
        })

        // 关闭 loading...
        this.loginLoading = false

        // 将接口返回的用户相关数据存储到本地
        window.localStorage.setItem('user', JSON.stringify(res.data.data))

        // 跳转到首页
        this.$router.push({
          name: 'home'
        })
      }).catch(err => {
        // 登陆失败
        console.log('登录失败', err) // 错误提示
        this.$message.error('登录失败，手机号或验证码错误')

        // 关闭 loading...
        this.loginLoading = false
      })
    }
  }
}
</script>

<style scoped lang="less">
.login-container {
  // 整个页面
  position: fixed;
  left: 0;
  top: 0;
  right: 0;
  bottom: 0;
  // form 内容
  display: flex; // 默认水平排列
  flex-direction: column; // 设置上下排列
  // form 水平居中
  justify-content: center;
  // form 垂直居中
  align-items: center;
  // 背景图片
  background: url("./login_bg.jpg") no-repeat;
  background-size: cover;
  .login-form-wrap {
    min-width: 300px;
    padding: 30px 50px 10px;
    background-image: linear-gradient(to top, #fdcbf1 0%, #fdcbf1 1%, #e6dee9 100%);
    .login-head {
      display: flex;
      justify-content: center;
      color: #5d8dfd;
      .logo {
        width: 200px;
        height: 57px;
        // background: url("./logo_index.png") no-repeat;
        background-size: contain;
      }
    }
    // form
    .login-form{
      .login-btn{
        width: 100%;
        background-image: linear-gradient(120deg, #a6c0fe 0%, #f68084 100%);
        }
    }
  }
}
</style>
