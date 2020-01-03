<template>
    <el-container class="home-container">
        <!--头部区域-->
        <el-header>
            <div>
                <img src="../assets/logo.png">
                <span>diyu管理系统</span>
            </div>
            <el-button type="info" @click="logout">退出</el-button>
        </el-header>
        <!--页面主体区-->
        <el-container>
            <!--侧边栏 router开启路由跳转模式（根据index跳转）this.$route.path（高亮的写法）-->
            <el-aside :width="isCollapse ? '64px' : '200px'">
                <div class="toggle-button" @click="toggleCollapse">||| </div>
                <el-menu background-color="#333744" text-color="#fff" active-text-color="#409EEF"
                         unique-opened :collapse="isCollapse" :collapse-transition="false"
                router :default-active="activePath">
                    <!--一级菜单-->
                    <el-submenu v-for="item in menuList" :index="'/'+item.url" :key="item.id">
                        <!--一级菜单模板区-->
                        <template slot="title">
                            <!--图标-->
                            <i class="el-icon-location"></i>
                            <!--文本-->
                            <span>{{item.menu_name}}</span>
                        </template>
                        <!-- 二级菜单 -->
                        <el-menu-item v-for="seconds in item.second" :index="'/'+seconds.url" :key="seconds.id" @click="saveNavState('/'+seconds.url)">
                            <!--一级菜单模板区-->
                            <template slot="title">
                                <!--图标-->
                                <i class="el-icon-menu"></i>
                                <!--文本-->
                                <span>{{seconds.menu_name}}</span>
                            </template>
                        </el-menu-item>
                    </el-submenu>
                </el-menu>
            </el-aside>
            <!--右侧内容主体-->
            <el-main>
                <!--路由占位符-->
                <router-view></router-view>
            </el-main>
        </el-container>
    </el-container>
</template>
<script>
    export default {
        data(){
            return{
                //左侧菜单数据
                menuList: [],
                //是否折叠
                isCollapse: false,
                //动态绑定的地址
                activePath:""
            }
        },
        created(){
          this.getMenuList()
          this.activePath = window.sessionStorage.getItem('activePath')
        },
        methods:{
            logout() {
                window.sessionStorage.clear()
                this.$router.push("/login")
            },
            async getMenuList(){
                const {data: res} = await this.$http.get('/menu/list')
                this.menuList = res.datas
            },
            //点击按钮
            toggleCollapse(){
                this.isCollapse = !this.isCollapse
            },
            //保存链接的激活状态
            saveNavState(activePath){
                window.sessionStorage.setItem('activePath',activePath)
                this.activePath = activePath
            }
        }
    }
</script>
<style lang="less" scoped>
    .home-container{
        height: 100%;
    }
    .el-header{
        background-color: #373d41;
        display: flex;
        justify-content: space-between;
        padding-left: 0;
        align-items: center;
        color: #fff;
        font-size: 20px;
        >div{
            display: flex;
            align-items: center;
            span {
                margin-left: 15px;
            }
        }
    }
    .el-aside{
        background-color: #333744;
        .el-menu{
            border-right: none;
        }
    }
    .el-main{
        background-color: #eaedf1;
    }
    .iconfoont{
        margin-right: 10px;
    }
    .toggle-button{
        background-color: #4A5064;
        font-size: 10px;
        height: 20px;
        color: #fff;
        text-align: center;
        letter-spacing: 0.2em;
        cursor: pointer;
    }
</style>
