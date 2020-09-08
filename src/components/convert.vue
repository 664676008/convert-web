<template>
    <el-container>
        <el-header>
            <img src="../assets/logo.png" alt="" height="100vh"/>
        </el-header>
        <el-main>
            <el-card>
                <el-form ref="form" :model="form" size="medium">

                    <div class="form-info">

                        <div style="float: left">
                            <el-form-item label="比特率" label-width="80px" style="margin-bottom: 60px;">
                                <el-slider
                                        @change="changeParam"
                                        v-model="value1"
                                        :marks="marks"
                                        :step="25"
                                        :format-tooltip="formatTooltip">
                                </el-slider>
                            </el-form-item>

                            <el-form-item label="采样率" label-width="80px" style="width: 450px;margin-bottom: 80px;">
                                <el-slider
                                        @change="changeParam"
                                        v-model="value2"
                                        :marks="marks2"
                                        :step="20"
                                        :format-tooltip="formatTooltip2">
                                </el-slider>
                            </el-form-item>

                            <el-form-item label="格式" label-width="80px" style="margin-bottom: 50px;">

                                <el-radio-group v-model="format" @change="changeParam">
                                    <el-radio v-model="format" label="1">MP3</el-radio>
                                    <el-radio v-model="format" label="2">FLAC</el-radio>
                                    <el-radio v-model="format" label="3">WAV</el-radio>
                                </el-radio-group>

                            </el-form-item>

                            <el-form-item v-if="showProgress" label="转换进度" label-width="80px" style="width: 450px;">
                                <el-progress :percentage="percentage" :color="customColors"></el-progress>
                            </el-form-item>

                            <el-form-item style="display: inline-block">

                                <el-button type="primary" @click="submitForm('form')">开始转换</el-button>
                                <el-button type="primary" @click="download" v-if="fileName">下载 ({{size}}M)</el-button>
                            </el-form-item>
                        </div>

                        <div class="upload">
                            <el-upload
                                    ref="upload"
                                    v-model="form.file"
                                    :data="form"
                                    :action="upload"
                                    :file-list="fileList"
                                    :on-remove="handleRemove"
                                    :on-change="handleChange"
                                    :on-success="handleSuccess"
                                    :auto-upload="false"
                                    :limit=1
                                    drag>
                                <i class="el-icon-upload"></i>

                                <div class="el-upload__text">将文件拖到此处，或<em>选择文件</em></div>
                            </el-upload>
                        </div>
                    </div>

                </el-form>
            </el-card>
        </el-main>
        <el-footer height="auto" style="font-size: 10px;">
            <div>
                <span style="margin-right: 10px;color: #535353">欢迎提出您的意见和建议 : zyt6646@126.com</span>
                <a href="https://beian.miit.gov.cn" target="_blank" style="color: #535353;"><img src="//img.alicdn.com/tps/TB1yEqRPXXXXXXPXpXXXXXXXXXX-20-20.png" style="position:relative;top:5px;vertical-align:baseline;">粤ICP备19108563号</a>
            </div>
        </el-footer>
    </el-container>

</template>

<script>
    export default {
        name: "Convert",
        data() {
            return {
                size: 0,
                showProgress: false,
                percentage: 0,
                customColors: [
                    {color: '#f56c6c', percentage: 20},
                    {color: '#e6a23c', percentage: 50},
                    {color: '#1989fa', percentage: 80},
                    {color: '#1989fa', percentage: 100},
                    {color: '#67c23a', percentage: 101}
                ],
                timer: null,
                fileName: "",
                key: null,
                upload: "https://www.zxzgs.com/zxzgs/convert/upload",
                format: '1',
                value1: 0,
                marks: {
                    0: '保持原状',
                    25: '128kb/s',
                    50: '256kb/s',
                    75: '512kb/s',
                    100: '1024kb/s'
                },
                value2: 0,
                marks2: {
                    0: '保持原状',
                    20: '11025Hz',
                    40: '22050Hz',
                    60: '24000Hz',
                    80: '44100Hz',
                    100: '48000Hz'
                },
                options: [],
                value: [],
                fileList: [],
                form: {
                    file: null,
                },
            }
        },
        methods: {
            //11025、22050、24000、44100、48000
            formatTooltip(val) {
                if (val == 0) {
                    return "保持原状";
                }else if (val == 25) {
                    return "128kb/s";
                }else if (val == 50) {
                    return "256kb/s";
                } else if (val == 75) {
                    return "512kb/s";
                } else if (val == 100) {
                    return "1024kb/s";
                }
            },
            formatTooltip2(val) {
                if (val == 0) {
                    return "保持原状";
                }else if  (val == 20) {
                    return "11025Hz";
                }else if (val == 40) {
                    return "22050Hz";
                } else if (val == 60) {
                    return "24000Hz";
                } else if (val == 80) {
                    return "44100Hz";
                } else if (val == 100) {
                    return "48000Hz";
                }
            },
            /**
             * 下载
             */
            download() {
                window.open("https://www.zxzgs.com/zxzgs/convert/download?key=" + this.key)
            },

            handleRemove() {
                this.form.file = null
            },
            handleChange(file) {
                this.form.file = file
                if (this.form.file.response) {
                    this.key = this.form.file.response.obj
                }
            },
            //提交表单
            changeParam() {
                let bit = 0;
                if(this.value1!=0){
                    let value = this.formatTooltip(this.value1)
                    bit = Number(value.substring(0, value.length - 4))*1000
                }
                let sampling = 0;
                if(this.value2!=0){
                    let value = this.formatTooltip2(this.value2)
                    sampling = value.substring(0, value.length - 2)
                }
                let format = 'mp3'
                if (this.format == 2) {
                    format = 'flac'
                } else if (this.format == 3) {
                    format = 'wav'
                }
                this.upload = "https://www.zxzgs.com/zxzgs/convert/upload?bit=" + bit + "&sampling=" + sampling + "&format=" + format
                console.log(this.$refs.upload.action)
            },
            submitForm() {
                this.showProgress = true
                if (this.$refs.upload.uploadFiles.length > 0) {
                    //校验成功 上传
                    this.$refs.upload.submit()
                } else {
                    this.$message({
                        message: '请选择文件',
                        type: 'warning'
                    });
                }
            },
            // 重置表单
            resetForm(formName) {
                this.$refs[formName].resetFields()
                this.$refs.upload.clearFiles()
            },
            handleSuccess() {
                this.resetForm('form')
                //上传成功,检查转换进度
                clearInterval(this.timer)
                this.timer = setInterval(this.listener, 500)
            },
            /**
             * 监听转化
             */
            listener() {
                let that = this
                this.$http.get("https://www.zxzgs.com/zxzgs/convert/getProgress", {
                    params: {
                        key: that.key
                    }
                })
                    .then(resp => {
                        that.percentage = parseFloat((resp.data.progress * 100).toFixed(2))
                        if (resp.data.progress == 1) {
                            clearInterval(that.timer)
                            that.fileName = resp.data.name
                            that.size = resp.data.size
                            this.$message({
                                message: '转码完成',
                                type: 'success'
                            });
                        }
                        //进度100时得到key
                    }).catch(err => {
                    console.log(err)
                })
            }
        }
    }
</script>

<style scoped>
    .form-info:after {
        content: '';
        display: inline-block;
        clear: both;
    }

    .el-card {
        width: 1000px;
        height: 420px;
        margin-top: 10vh;
        padding-top: 15px;
    }

    .el-main {
        display: flex;
        justify-content: center;
        align-items: center;
    }
.upload{
    width: 450px;
    float: right;
}
</style>
<style>
    .el-radio__input.is-checked+.el-radio__label{
        color: #44c299 !important;
    }
    .el-radio__input.is-checked .el-radio__inner{
        background: #44c299 !important;
        border-color: #44c299 !important;
    }
    .el-tooltip.el-slider__button{
        border:2px solid #44c299 !important;
    }
    .el-slider__bar{
        background-color:#44c299 !important;
    }
    .el-upload-dragger .el-upload__text em{
        color:#44c299 !important;
    }
    .el-upload-dragger:hover{
        border:1px solid #44c299 !important;
    }
    .el-upload-dragger{
        border:1px dashed #44c299 !important;
    }
    .el-icon-upload{
        color:#44c299 !important;
    }
    .el-icon-upload:hover{
        color:#45deb5 !important;
    }
    .el-card{
        border:1px solid #44c299 !important;
    }
    .el-form-item__label{
        position: relative;
        right: 20px;
    }
    .el-button.el-button--primary.el-button--medium{
        background-color: #44c299;
        border: 1px solid #44c299;
    }
    .el-button.el-button--primary.el-button--medium:hover{
        background-color: #45deb5;
        border: 1px solid #45deb5;
    }
    .el-footer {
        z-index: 0;
        bottom: 0;
        width: 100%;
        height: 5vh !important;
        position: fixed;
    }
</style>
