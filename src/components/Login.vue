<template>
    <div class="login_container">
        <div class="login_box">
            <!-- 头像区域 -->
            <div class="avatar_box">
                <img src="../assets/logo.png" alt="">
            </div>
            <!-- 登录表单区域 ref="form" :model="form" -->
            <el-form class="login_form" :rules="rules" :model="userLogin" ref="loginFormRef">
                <el-form-item prop="username">
                    <el-input v-model="userLogin.username" prefix-icon="el-icon-s-custom"></el-input>
                </el-form-item>
                <el-form-item prop="password">
                    <el-input placeholder="请输入密码" v-model="userLogin.password" show-password prefix-icon="el-icon-lock"></el-input>
                </el-form-item>
                <!--按钮-->
                <el-form-item class="btns">
                    <el-button type="primary" @click="login">登录</el-button>
                    <el-button type="info" @click="resetLoginForm">重置</el-button>
                </el-form-item>

            </el-form>
        </div>
    </div>
</template>
<script>
    export default {
        data(){
            return{
                //这是登录表单的数据绑定对象
                userLogin: {
                    username: "zhang",
                    password: "123456"
                },
                //这是表单的验证规则对象
                rules:{
                    //验证用户名是否合法
                    username: [
                        { required: true, message: '请输入用户名', trigger: 'blur' },
                        { min: 2, max: 10, message: '长度在 2 到 10个字符', trigger: 'blur' }
                    ],
                    //验证密码是否合法
                    password: [
                        { required: true, message: '请输入登录密码', trigger: 'blur' },
                        { min: 6, max: 15, message: '长度在 6 到 15 个字符', trigger: 'blur' }
                    ]
                }
            }
        },
        methods: {
            // 点击重置按钮，重置登陆表单
            resetLoginForm(){
                this.$refs.loginFormRef.resetFields();
            },
            login(){
                //表单的预校验
                this.$refs.loginFormRef.validate(async valid =>{
                    if(!valid) return;
                    const {data: res} = await this.$http.post('login',this.userLogin)
                    if(res.code == 1){
                        this.$message.success(res.datas)
                        console.log("res："+ JSON.stringify(res))
                        //登录之后的token 后台生成token
                        window.sessionStorage.setItem('token',"ni jiu shi w d token")
                        this.$router.push('/home')
                    }else{
                        this.$message.error(res.datas)
                    }
                })
            }
        }
    }
</script>
<style lang="less" scoped>
    .login_container{
        background-color: #4b5b6c;
        height: 100%;
    }
    .login_box{
        width: 450px;
        height: 300px;
        background-color: #fff;
        border-radius: 3px;
        position: absolute;
        left: 50%;
        top: 50%;
        transform: translate(-50%,-50%);
        .avatar_box{
            height: 130px;
            width: 130px;
            border: 1px solid #eee;
            border-radius: 50%;
            padding: 10px;
            box-shadow: 0 0 10px #ddd;
            position: absolute;
            left: 50%;
            transform: translate(-50%,-50%);
            background-color: white;
            img{
                width: 100%;
                height: 100%;
                border-radius: 50%;
                background-color: #eee;
            }
        }
    }
    .login_form{
        position: absolute;
        bottom: 0;
        width: 100%;
        padding: 0 20px;
        box-sizing: border-box;
    }
    .btns{
        display: flex;
        justify-content: flex-end;
    }
</style>
