<template>
    <div class="container">
        <el-form :model="ruleForm" :rules="rules" ref="ruleForm" label-width="90px" class="demo-ruleForm">
            <el-form-item label="用户名" prop="user_name">
                <el-input v-model="ruleForm.user_name" placeholder="请输入用户名"></el-input>
            </el-form-item>
            <el-form-item label="姓名" prop="name">
                <el-input v-model="ruleForm.name" placeholder="请输入姓名"></el-input>
            </el-form-item>
           <el-form-item label="寝室号" prop="location">
                <el-input v-model="ruleForm.location" placeholder="请输入寝室号"></el-input>
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
                <el-button @click="resetForm('ruleForm')" class="isjs-ac">清空</el-button>
            </el-form-item>
        </el-form>
    </div>
</template>

<script>
    import Vue from 'vue'
    import {checkUsername, checkName, checkLocation, checkRemark} from '../../components/reg.js'
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
                rules: {
                    user_name:[
                        {required: true, message: '请输入用户名', trigger: 'change'},
                        {validator: checkUsername, trigger: 'change'}
                    ],
                    name:[
                        {required: true, message: '请输入姓名', trigger: 'change'},
                        {validator: checkName, trigger: 'change'}
                    ],
                    location:[
                        {required: true, message: '请输入寝室号', trigger: 'change'},
                        {validator: checkLocation, trigger: 'change'}
                    ],
                    state:[
                        {required: true, message: '请选择状态', trigger: 'change'}
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
                        this.$axios({
                            method: "post",
                            url: "/api/dorm/create",
                            withCredentials: true,
                            data: {
                                user_name: this.ruleForm.user_name,
                                name:this.ruleForm.name,
                                location:this.ruleForm.location,
                                state:this.ruleForm.state,
                                remark:this.ruleForm.remark
                            },
                            header: {
                                "Content-Type": "application/json;charset=UTF-8"
                            }
                        }).then(res => {
                            this.$message({
                                type:'success',
                                message:'学生数据添加成功!'
                            });
                            this.$parent.$parent.successAdd();
                        }).catch(error => {
                            if(error.response.status == '500'){
                                this.$message({
                                    type:'error',
                                    message:'该学生的后勤处已存在/该学生存在，请重新上传！'
                                });
                                this.resetForm('ruleForm');
                            }
                        });
                    } else {
                        this.$message({
                            type:'error',
                            message:'提交数据失败，请重新提交！'
                        });
                    }
                });
            },
            resetForm(formName) {
                this.$refs[formName].resetFields();
            }
        },
        mounted() {
            doCollect();
        }
    }
</script>

<style scoped>

</style>