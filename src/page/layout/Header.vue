<template>
    <div class="header">
        <div class="logo">
            <slot name="logo"></slot>
        </div>
        <slot name="topnav"></slot>
        <div class="userInfo">
            <ul>
                <li>
                    <el-dropdown @command="userOperation">
                        <span class="user">{{username}}<i class="el-icon-caret-bottom el-icon--right"></i></span>
                        <el-dropdown-menu slot="dropdown">
                            <el-dropdown-item command="editPaw">{{$t('global.editpassword')}}</el-dropdown-item>
                            <el-dropdown-item command="logout">{{$t('global.logout')}}</el-dropdown-item>
                        </el-dropdown-menu>
                    </el-dropdown>
                </li>
            </ul>
        </div>
        <el-dialog title="修改密码" :visible.sync="dialog.editPaw.show" :modal-append-to-body="false" custom-class="editPawDialog">
            <el-form :model="editPaw" :rules="editPawRules" ref="editPaw" label-width="100px" >
                <el-form-item label="旧密码" prop="oldPaw">
                    <el-input v-model="editPaw.oldPaw" auto-complete="off"></el-input>
                </el-form-item>
                <el-form-item label="新密码" prop="newPaw">
                    <el-input v-model="editPaw.newPaw" auto-complete="off"></el-input>
                </el-form-item>
                <el-form-item label="确认新密码" prop="confirmNewPaw">
                    <el-input v-model="editPaw.confirmNewPaw" auto-complete="off"></el-input>
                </el-form-item>
            </el-form>
            <div class="textC">
                <el-button type="primary" @click="editPawSubmit">保存</el-button>
            </div>
        </el-dialog>
    </div>
    
</template>

<script>
import { mapState, mapMutations, mapActions } from 'Vuex'

export default {
    data() {
        return {
            dialog: {
                editPaw: {
                    show: false
                }
            },
            editPaw: {
                oldPaw: '',
                newPaw: '',
                confirmNewPaw: ''
            },
            editPawRules: {
                oldPaw: [
                    {required: true, message: '请输入旧密码', trigger: 'blur'}
                ],
                newPaw: [
                    {required: true, message: '请输入新密码', trigger: 'blur'},
                    {min: 8, max: 20, message: '长度在 8 到 20 个字符', trigger: 'blur'},
                    {
                        // eslint-disable-next-line
                        validator(rule, value, callback, source, options) {
                            var errors = [];
                            if(!/^[a-z0-9]+$/.test(value)) {
                                console.log("不符合输入规则")
                                errors.push("请输入字母或特殊字符")
                            }
                            callback(errors);
                        }
                    }
                ],
                confirmNewPaw: [
                    {required: true, message: '请再次输入新密码', trigger: 'blur'},
                    {min: 8, max: 20, message: '长度在 8 到 20 个字符', trigger: 'blur'},
                    {
                        // eslint-disable-next-line
                        validator(rule, value, callback, source, options) {
                            var errors = [];
                            if(!/^[a-z0-9]+$/.test(value)) {
                                console.log("不符合输入规则")
                                errors.push("请输入字母或特殊字符")
                            }
                            callback(errors);
                        }
                    }
                ]
            }
        }
    },
    computed: {
        ...mapState({
            username: state => state.user.name,
            lang: state => state.lang
        })
    },
    methods: {
        ...mapMutations({
            toggleLang: 'changeLang'
        }),
        ...mapActions({
            sysLogout: 'user/logout'
        }),
        changeLang(val) {
            if (val == this.lang) return
            this.toggleLang(val)
        },
        userOperation(command){
            switch(command){
                case 'logout': 
                    this.logout()
                    break
                case 'editPaw':
                    this.dialog.editPaw.show = true
                    console.log('编辑密码')
                    break
            }
        },
        logout() {
            this.sysLogout().then(() => {
                this.$router.push('/login')
            })
        },
        editPawSubmit(){
            this.$refs.editPaw.validate((valid) => {
                if (valid) {
                    console.log("修改密码表单提交")
                } else {
                    console.log('error submit!!');
                    return false;
                }
            });
        }
    }
}
</script>

<style lang="scss" scoped>
@import '~sysStatic/css/theme/theme.scss';
.header {
    // position: relative;
    width: 100%;
    height: 60px;
    background: $headerBgColor;
    z-index: 500;
    .logo {
        float: left;
        height: $headerHeight;
        line-height: $headerHeight;
        padding-left: 20px;
        color: $white;
        font-size: 20px;
    }
}

.userInfo {
    float: right;
    height: 60px;
    &>ul {
        height: 60px;
        &>li {
            display: block;
            float: left;
            height: 60px;
            margin-right: 20px;
            color: $white;
            .lang {
                line-height: 60px;
                color: $lightGray;
                cursor: pointer;
                &.cur {
                    color: $white;
                }
            }
            .user {
                display: block;
                height: 60px;
                line-height: 60px;
                color: $white;
                cursor: pointer;
            }
        }
    }
}
</style>