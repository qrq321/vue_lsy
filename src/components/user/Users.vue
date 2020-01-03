<template>
    <div>
        <!--面包屑导航-->
        <el-breadcrumb separator-class="el-icon-arrow-right">
            <el-breadcrumb-item :to="{ path: '/home' }">首页</el-breadcrumb-item>
            <el-breadcrumb-item>用户管理</el-breadcrumb-item>
            <el-breadcrumb-item>用户列表</el-breadcrumb-item>
        </el-breadcrumb>
        <!--卡片视图区域-->
        <el-card>
                <!--搜索与添加区-->
            <el-row :gutter="20">
                <el-col :span="7">
                    <el-input placeholder="请输入内容" class="input-with-select">
                        <el-button slot="append" icon="el-icon-search"></el-button>
                    </el-input>
                </el-col>
                <el-col :span="4">
                    <el-button type="primary">添加用户</el-button>
                </el-col>
            </el-row>

            <!--用户列表区域-->
            <el-table :data="userList">
                <el-table-column type="index"></el-table-column>
                <el-table-column label="账号" prop="account"></el-table-column>
                <el-table-column label="状态" prop="status">
                    <template slot-scope="scope">
                        <el-switch
                                v-model="scope.row.status">
                        </el-switch>
                    </template>
                </el-table-column>
                <el-table-column label="操作">
                    <template>
                        <!--修改按钮-->
                        <el-button type="primary" icon="el-icon-edit" size="mini"></el-button>
                        <!-- 删除按钮-->
                        <el-button type="danger" icon="el-icon-delete" size="mini"></el-button>
                        <!--分配角色按钮-->
                        <el-tooltip effect="dark" content="分配角色" placement="top" :enterable="false">
                            <el-button type="warning" icon="el-icon-setting" size="mini"></el-button>
                        </el-tooltip>
                    </template>
                </el-table-column>
                <el-table-column label="操作"></el-table-column>
            </el-table>
            <!--分页区-->
            <el-pagination
                    @size-change="handleSizeChange"
                    @current-change="handleCurrentChange"
                    :current-page="queryInfo.pageNum"
                    :page-sizes="[10, 20, 50, 100]"
                    :page-size="queryInfo.pageSize"
                    layout="total, sizes, prev, pager, next, jumper"
                    :total="total">
            </el-pagination>
        </el-card>
    </div>
</template>
<script>
    export default {
        data(){
            return {
                //获取用户列表参数对象
                queryInfo: {
                    query: "",
                    //当前的页数
                    pageNum: 1,
                    //当前每页显示多少条数据
                    pageSize: 2
                },
                userList: [],
                total: 0
            }
        },
        created(){
            this.getUserList()
        },
        methods:{
            async getUserList(){
                const res = await this.$http.post('users/list',this.queryInfo)
                const record = res.data.datas
                console.log("record:"+JSON.stringify(record))
                this.userList = record.records
                this.total = record.total

            },
            handleSizeChange(newSize){
                console.log(newSize)
                this.queryInfo.pageSize = newSize
            },
            handleCurrentChange(newPage){
                console.log(newPage)
                this.queryInfo.pageNum = newPage
            }
        }
    }
</script>
<style xml:lang="less" scoped>

</style>
