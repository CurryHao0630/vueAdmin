<template>
    <el-row type="flex" class="row-bg" justify="center">
        <el-col :xl="6" :lg="7">
            <h2>欢迎来到VueAdmin管理系统</h2>
            <el-image :src="require('@/assets/ch.jpg')" style="height: 180px; width: 180px;"></el-image>
            <p>微信号: CH041913</p>
            <p>扫描二维码，回复【VueAdmin】获取登录密码</p>
        </el-col>
        <el-col :span="1">
            <el-divider direction="vertical"></el-divider>
        </el-col>
        <el-col :xl="6" :lg="7">
            <el-form :model="loginForm" :rules="rules" ref="loginForm" label-width="100px" class="demo-loginForm">
                <el-form-item label="用户名" prop="username" style="width: 380px">
                    <el-input v-model="loginForm.username"></el-input>
                </el-form-item>
                <el-form-item label="密码" prop="password" style="width: 380px">
                    <el-input v-model="loginForm.password" type="password"></el-input>
                </el-form-item>
                <el-form-item label="验证码" prop="code" style="width: 380px">
                    <el-input v-model="loginForm.code" style="width: 152px; float: left"></el-input>
                    <el-image :src="captchaImg" class="captchaImg" @click="getCaptcha"></el-image>
                </el-form-item>
                <el-form-item>
                    <el-button type="primary" @click="submitForm('loginForm')">登录</el-button>
                    <el-button @click="resetForm('loginForm')">重置</el-button>
                </el-form-item>
            </el-form>
        </el-col>
    </el-row>
</template>

<script>
    import qs from 'qs'

    export default {
        name: "Login",
        data() {
            return {
                loginForm: {
                    username: '',
                    password: '',
                    code: '',
                    token: ''
                },
                rules: {
                    username: [
                        { required: true, message: '请输入用户名', trigger: 'blur' },
                        { min: 1, max: 15, message: '长度在 3 到 15 个字符', trigger: 'blur' }
                    ],
                    password: [
                        { required: true, message: '请输入密码', trigger: 'blur' }
                    ],
                    code: [
                        { required: true, message: '请输入验证码', trigger: 'blur' },
                        { min: 5, max: 5, message: '长度为 5 个字符', trigger: 'blur' }
                    ]
                },
                captchaImg: null
            };
        },
        methods: {
            submitForm(formName) {
                this.$refs[formName].validate((valid) => {
                    if (valid) {
                        console.log("valid")
                        // this.$axios.post('/login', this.loginForm).then(res => {
                        this.$axios.post('/login?'+ qs.stringify(this.loginForm)).then(res => {
                            // console.log("res")
                            // console.log(res)

                            const jwt = res.headers['authorization']

                            this.$store.commit("SET_TOKEN", jwt)

                            // this.$store.commit("SET_USERINFO", res.data.data)

                            this.$router.push("/index")

                        })
                    } else {
                        console.log('error submit!!');
                        return false;
                    }
                });
            },
            resetForm(formName) {
                this.$refs[formName].resetFields();
            },
            getCaptcha() {
                this.$axios.get("/captcha").then(res => {

                    // console.log("/captcha")
                    // console.log(res)

                    this.loginForm.token = res.data.data.token
                    this.captchaImg = res.data.data.captchaImg //base64（字符串）形式的图片

                    this.loginForm.code = ''
                })
            }
        },
        created() {
            this.getCaptcha()
        }
    }
</script>

<style scoped>
    .el-divider {
        height: 220px;
    }

    .el-row {
        background-color: #fafafa;
        height: 100%;
        display: flex;
        align-items: center;
        text-align: center;
    }

    .captchaImg {
        float: left;
        margin-left: 8px;
        border-radius: 4px;
    }
</style>