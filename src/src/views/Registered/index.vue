<template>
    <div>
        <v-header :activeIndex="activeIndex"></v-header>
        <el-row class="box">
            <el-col :span="12" :offset="6">
                <el-form :model="ruleForm2" status-icon :rules="rules2" ref="ruleForm2" label-width="100px" class="demo-ruleForm">
                    <el-form-item label="账户" prop="username">
                        <el-input v-model="ruleForm2.username"></el-input>
                    </el-form-item>
                    <el-form-item label="邮箱" prop="email">
                        <el-input v-model="ruleForm2.email"></el-input>
                    </el-form-item>
                    <el-form-item label="密码" prop="pass">
                        <el-input type="password" v-model="ruleForm2.pass" autocomplete="off"></el-input>
                    </el-form-item>
                    <el-form-item label="确认密码" prop="checkPass">
                        <el-input type="password" v-model="ruleForm2.checkPass" autocomplete="off"></el-input>
                    </el-form-item>
                    <el-form-item>
                        <el-button type="primary" @click="submitForm('ruleForm2')">注册</el-button>
                        <el-button @click="resetForm('ruleForm2')">重置</el-button>
                    </el-form-item>
                </el-form>
            </el-col>
        </el-row>
    </div>
</template>

<script>
    import axios from '@/utils/axios'
    import { setToken } from '@/utils/cookie'
    import header from '@/components/header/header'

    export default {
        data() {
            // 密码验证
            let validatePass = (rule, value, callback) => {
                if (value === '') {
                callback(new Error('请输入密码'))
                } else {
                    let reg = /(?!^[0-9]+$)(?!^[A-z]+$)(?!^[^A-z0-9]+$)^[^\s\u4e00-\u9fa5]{6,16}$/
                    if (!reg.test(value)) {
                        callback(new Error("密码长度需6-16位，且包含字母和字符"))
                    } else {
                        callback();
                    }
                }
            };
            let validatePass2 = (rule, value, callback) => {
                if (value === '') {
                    callback(new Error('请再次输入密码'));
                } else if (value !== this.ruleForm2.pass) {
                    callback(new Error('两次输入密码不一致!'));
                } else {
                    callback();
                }
            };
            // 邮箱验证
            let validateEmail = (rule, value, callback) => {
                if (value === '') {
                    callback(new Error('请输入邮箱账号'))
                }else {
                    let reg = /^[a-zA-Z0-9_.-]+@[a-zA-Z0-9-]+(\.[a-zA-Z0-9-]+)*\.[a-zA-Z0-9]{2,6}$/
                    if (!reg.test(value)) {
                        callback(new Error("邮箱格式不正确"))
                    } else {
                        callback();
                    }
                }
            }
            return {
                activeIndex: '/registered',
                ruleForm2: {
                    pass: '',
                    checkPass: '',
                    username: '',
                    email: '',
                },
                rules2: {
                    pass: [
                        {  required: true, validator: validatePass, trigger: 'blur' },
                        {
                            min: 6,
                            max: 12,
                            message: "密码必须在6-12位之间",
                        }
                    ],
                    checkPass: [
                        {  required: true, validator: validatePass2, trigger: 'blur' }
                    ],
                    username: [
                        { required: true, message: "请输入用户名", trigger: "blur" },
                        {
                            min: 6,
                            max: 16,
                            message: "用户名必须在6-16位之间",
                        }
                    ],
                    email: [
                        { required: true, validator: validateEmail, trigger: 'blur' }
                    ]
                }
            };
        },
        methods: {
            submitForm(formName) {
                this.$refs[formName].validate((valid) => {
                    if (valid) {
                        axios({
                            url: '/registered',
                            method: 'post',
                            data: this.ruleForm2
                        }).then(({ data }) => {
                            // 设置token值
                            setToken(data.data.token)
                            this.$message({ // 消息提示
                                message: data.message,
                                type: 'success'
                            });
                            // 500毫秒后跳转到首页
                            setTimeout(() => {
                                this.$router.push('/')
                            }, 500);
                        })
                    } else {
                        this.$message({ // 消息提示
                            message: '注册失败',
                            type: 'warning'
                        });
                        return false;
                    }
                });
            },
            resetForm(formName) {
                this.$refs[formName].resetFields();
            }
        },
        components: {
            'v-header': header
        }
    }
</script>

<style scoped lang="less">
    .box {
        background-color: rgb(241, 241, 241);

        .demo-ruleForm {
            padding: 30px 0px;
        }
    }

</style>
