<template>
    <div class="container">
        <el-form :model="ruleForm" :rules="rules" ref="ruleForm" label-width="90px" class="demo-ruleForm">
            <el-form-item label="用户名" prop="user_name">
                <el-input v-model="ruleForm.user_name" disabled></el-input>
            </el-form-item>
            <el-form-item label="姓名" prop="name">
                <el-input v-model="ruleForm.name" disabled></el-input>
            </el-form-item>
            <el-form-item label="寝室号" prop="location">
                <el-input v-model="ruleForm.location" disabled></el-input>
            </el-form-item>
            <el-form-item label="后勤状态" prop="state">
                <el-select v-model="ruleForm.state" placeholder="请选择">
                    <el-option
                        v-for="item in states"
                        :key="item.value"
                        :label="item.label"
                        :value="item.value">
                        <span style="float: left">{{ item.label }}</span>
                    </el-option>
                </el-select>
            </el-form-item>
            <el-form-item label="备注" prop="remark">
                <el-input v-model="ruleForm.remark" placeholder="请输入备注，如没有备注信息，请输入无"></el-input>
            </el-form-item>
            <el-form-item style="text-align:center;margin-left:-70px;">
                <el-button type="primary" @click="submitForm('ruleForm')" class="isjs-ac">立即修改</el-button>
            </el-form-item>
        </el-form>
    </div>
</template>

<script>
    import Vue from 'vue'
    import {checkRemark} from '../../components/reg.js'
    import {doCollect} from '../dataAcquisition.js'
    export default {
        data() {
            return {
                 states: [
                    {
                        value: '0',
                        label: '未完成'
                    },
                    {
                        value: '1',
                        label: '已完成'
                    }
                ],
                ruleForm: {},
                // 表单验证规则
                rules: {
                    user_name: [
                        { required: true, message: '请输入用户名', trigger: 'change' }
                    ],
                    name: [
                        { required: true, message: '请输入姓名', trigger: 'change' }
                    ],
                    location: [
                        { required: true, message: '请输入后勤状态', trigger: 'change' }
                    ],
                    state: [
                        { required: true, message: '请选择后勤状态', trigger: 'change' }
                    ],
                    remark: [
                        { required: true, message: '请输入备注', trigger: 'change' },
                        {validator: checkRemark, trigger: 'change'}
                    ]
                }
            };
        },
        methods: {
            submitForm(formName) {
                this.$refs[formName].validate((valid) => {
                    if (valid) {
                        this.$confirm('此操作将修改学生信息, 是否继续?', '提示',{
                            confirmButtonText: '确定',
                            cancelButtonText: '取消',
                            type: 'warning' 
                        }).then(() => {
                            this.$axios({
                                method: "post",
                                url: "/api/dorm/update/" + this.ruleForm.id,
                                withCredentials: true,
                                data: {
                                    id:this.ruleForm.id,
                                    user_name: this.ruleForm.user_name,
                                    location: this.ruleForm.location,
                                    state: this.ruleForm.state,
                                    remark: this.ruleForm.remark,
                                },
                                header: {
                                    "Content-Type": "application/json;charset=UTF-8"
                                }
                            }).then(res=>{
                                this.$message({
                                    type: 'success',
                                    message: '修改信息成功'
                                });
                                this.$parent.$parent.successEdit();
                            }).catch(error=>{
                                this.$message({
                                    type: 'error',
                                    message: '请求错误，修改信息失败！'
                                });
                            })
                        }).catch(() => {
                                this.$message({
                                type: 'info',
                                message: '已取消修改'
                            });          
                        });
                    } else {
                        this.$message({
                            type: 'error',
                            message: '请求错误，修改信息失败！'
                        });
                        return false;
                    }
                });
            },
            getRowMsg() {
                var JsonStr = sessionStorage.getItem('rowDormMsg');
                this.ruleForm = JSON.parse(JsonStr);
            }
        },
        mounted() {
            doCollect();
            this.getRowMsg();
        }
    }
</script>

<style scoped>

</style>