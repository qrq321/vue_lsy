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
                    <el-input placeholder="请输入内容" clearable v-model="queryInfo.query" @clear="getUserList" class="input-with-select">
                        <el-button slot="append" icon="el-icon-search" @click="getUserList"></el-button>
                    </el-input>
                </el-col>
                <el-col :span="4">
                    <el-button type="primary" @click="add">添加用户</el-button>
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
                    <template slot-scope="scope">
                        <!--修改按钮-->
                        <el-button type="primary" icon="el-icon-edit" size="mini" @click="edit(scope.row.id)"></el-button>
                        <!-- 删除按钮-->
                        <el-button type="danger" icon="el-icon-delete" size="mini" @click="deletes(scope.row.id)"></el-button>
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

            <!--添加-->
            <el-dialog :title="userFunc" :close-on-click-modal="false" @close="dialogClosed" :visible.sync="dialogVisible" width="50%">
                <!--内容主体区-->
                <el-form :model="accountForm" :rules="formRules"
                         ref="formRef" label-width="80px" >
                    <!--account 验证规则-->
                    <el-form-item label="主键" v-show="false" prop="id">
                        <el-input v-model="accountForm.id"></el-input>
                    </el-form-item>
                    <el-form-item label="账号" prop="account">
                        <el-input v-model="accountForm.account"></el-input>
                    </el-form-item>
                    <el-form-item label="密码" prop="password">
                        <el-input type="password" show-password v-model="accountForm.password"></el-input>
                    </el-form-item>
                    <el-form-item label="用户名" prop="userName">
                        <el-input v-model="accountForm.userName"></el-input>
                    </el-form-item>
                    <el-form-item label="电话号码" prop="phone">
                        <el-input v-model="accountForm.phone"></el-input>
                    </el-form-item>

                </el-form>

                <span slot="footer" class="dialog-footer">
                    <el-button @click="dialogVisible = false">取 消</el-button>
                    <el-button type="primary" @click="addUser">确 定</el-button>
                </span>
            </el-dialog>
        </el-card>
    </div>
</template>
<script>
    export default {
        data(){
            var validPhone=(rule, value,callback)=>{
                if (!value){
                    callback(new Error('请输入电话号码'))
                }else  if (!isvalidPhone(value)){
                    callback(new Error('请输入正确的11位手机号码'))
                }else {
                    callback()
                }
            }
            /*//定义邮箱验证规则
            var checkEmail=((rule, value,callback)=>{
                    const regEmail = /^([a-zA-Z0-9_-])+@([a-zA-z0_-])+(\.[a-zA-Z009_-])+/
                    if(regEmail.test(value)){
                        //合法邮箱
                        return callback()
                    }
                    callback(new Error("输入合法的邮箱"))
                })*/
            return {
                //获取用户列表参数对象
                queryInfo: {
                    query: "",
                    //当前的页数
                    pageNum: 1,
                    //当前每页显示多少条数据
                    pageSize: 10
                },
                userList: [],
                total: 0,
                userFunc: "添加用户",
                //控制添加对话框的显示与隐藏
                dialogVisible: false,
                //添加用户的表单数据
                accountForm: {
                    id:'',
                    account: '',
                    password: '',
                    userName: '',
                    phone: ''
                },
                //添加表单的验证规则对象
                formRules: {
                    id:[],
                    account:[
                        {required:true,message: '请输入用户名',trigger:'blur'},
                        {min:2,max:10,message:'用户名的长度在2~10个字符之间',trigger: 'blur'}
                    ],
                    password:[
                        {required:true,message: '请输入密码',trigger:'blur'},
                        {min:6,max:15,message:'密码在6~15个字符之间',trigger: 'blur'}
                    ],
                    userName:[
                        {required:true,message: '请输入用户名',trigger:'blur'},
                        {min:2,max:10,message:'用户名的长度在3~10个字符之间',trigger: 'blur'}
                    ],
                    phone: [
                        { required: true, trigger: 'blur', validator: validPhone }//这里需要用到全局变量
                    ]
                }
            }
        },
        created(){
            this.getUserList()
        },
        methods:{
            //初始化列表数据
            async getUserList(){
                const res = await this.$http.post('users/list',this.queryInfo)
                const record = res.data.datas
                this.userList = record.records
                this.total = record.total
            },
            handleSizeChange(newSize){
                this.queryInfo.pageSize = newSize
                this.getUserList()
            },
            handleCurrentChange(newPage){
                this.queryInfo.pageNum = newPage
                this.getUserList()
            },
            async add(){
                this.userFunc = "添加用户"
                this.dialogVisible = true
            },
            async edit(id){
                this.userFunc = "修改用户"
                //打开form表单放在加载数据之前，不然清空会报错
                this.dialogVisible = true
                const res = await this.$http.get('users/edit/'+id)
                const record = res.data.datas
                this.accountForm = record
            },
            async deletes(id){
                console.log(id)
                this.$confirm('删除该用户, 是否继续?', '提示', {
                    confirmButtonText: '确定',
                    cancelButtonText: '取消',
                    type: 'warning'
                }).then(async() => {
                    const res =await this.$http.delete('users/delete/'+id)
                    if(res.data.code == 1){
                        this.getUserList()
                        this.$notify({
                            title: '删除成功',
                            position: 'bottom-right',
                            type: 'success',
                            time: '2'
                        });
                    }else{
                        this.$notify({
                            title: '删除失败！',
                            position: 'bottom-right',
                            type: 'false',
                            time: '2'
                        });
                    }
                }).catch(() => {
                    this.$notify({
                        title: '取消删除',
                        position: 'bottom-right',
                        type: 'info',
                        time: '2'
                    });
                });
            },
            //监听添加用户对话框的关闭事件
            dialogClosed(){
                this.$refs.formRef.resetFields()
                console.log('close')
            },
            async addUser(){
                var _this = this;
                this.$refs.formRef.validate(async valid=>{
                    if(!valid) return
                    const res =await this.$http.post('users/add',this.accountForm)
                    if(res.data.code==1){
                        this.dialogVisible = false
                        this.getUserList()
                        this.$notify({
                            message: _this.userFunc+'成功！',
                            position: 'bottom-right',
                            type: 'success',
                            time: '2'
                        });
                    }else{
                        this.$notify({
                            message: _this.userFunc+'失败！',
                            position: 'bottom-right',
                            type: 'false',
                            time: '2'
                        });
                    }
                })
            }

        }
    }
    // 手机号验证
    export function isvalidPhone(str) {
        const reg = /^1[3|4|5|7|8][0-9]\d{8}$/
        return reg.test(str)
    }
</script>
<style xml:lang="less" scoped>

</style>
