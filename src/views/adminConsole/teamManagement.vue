<template>
    <div id="teamManagement">
        <el-form>
            <el-row>
                <el-col :span="12">
                    <el-form-item>
                        <el-select v-model="yearValue"
                                   placeholder="比赛年份"
                                   size="small"
                                   style="width: 100px;margin-right: 20px"
                                   @change="handleYearChange">
                            <el-option
                                    v-for="item in competitionYearOptions"
                                    :key="item.value"
                                    :label="item.label"
                                    :value="item.value">
                            </el-option>
                        </el-select>
                        <el-select v-model="stageValue"
                                   placeholder="请选择比赛阶段"
                                   size="small"
                                   @change="getFilesSignUp">
                            <el-option
                                    v-for="item in stageOptions"
                                    :key="item.value"
                                    :label="item.label"
                                    :value="item.value">
                            </el-option>
                        </el-select>
                        <el-button @click="selectJudges = true" size="small" style="margin-left: 22px;color: #909399">关联评委{{tnames}}</el-button>
                    </el-form-item>
                    <hr class="dif3"/>
                </el-col>
                <el-col :span="8" :offset="4" class="dif2">
                    <el-form-item class="pull-right">
                        <el-select v-model="searchKeyValue"
                                   placeholder="搜索关键字"
                                   size="small"
                                   style="width: 100px;line-height: 40px;margin-right: 10px">
                            <el-option
                                    v-for="item in searchOptions"
                                    :key="item.value"
                                    :label="item.label"
                                    :value="item.value">
                            </el-option>
                        </el-select>
                        <el-input placeholder="请输入搜索内容" v-model="search" size="small" style="width: 130px"></el-input>
                        <hr class="dif4">
                        <span class="dif4">请右划查看相关信息</span>
                    </el-form-item>

                </el-col>
            </el-row>
        </el-form>

        <el-dialog
                :visible.sync="selectJudges">
            <el-form class="sform">
                <el-form-item label="教师工号">
                    <el-input style="display: block; width: 50%"
                              v-model="selectedTeacherId"
                              placeholder="请输入教师工号">
                    </el-input>
                </el-form-item>
                <el-form-item label="教师姓名">
                    <el-input style="display: block; width: 50%"
                              v-model="selectedTeacherName"
                              placeholder="请输入教师姓名">
                    </el-input>
                </el-form-item>
                <el-form-item label="查询教师">
                    <el-button @click="getT" round>查询并选择</el-button>
                </el-form-item>
                <el-form-item label="查询结果">
                    <span v-for="item in getTeachers" :key="item.teacherId"><span>{{item.teacherName+','}}</span></span>
                </el-form-item>
                <el-form-item label="关联查询到的第一个评委">
                    <el-button round @click="cRelatedT">关联/取消关联</el-button>
                </el-form-item>
            </el-form>
        </el-dialog>

        <el-table
                ref="multipleTable"
                :data="searchInfo()"
                tooltip-effect="dark"
                stripe
                style="width: 100%;height: 60vh;overflow: auto"
                :default-sort = "{prop: 'projectName', order: 'descending'}"
                @selection-change="handleChange">

            <el-table-column
                    type="selection"
                    width="50"
                    align="center">
            </el-table-column>

            <el-table-column type="expand">
                <template slot-scope="props">
                    <el-form label-position="left" class="demo-table-expand">
                        <el-form-item label="队伍名称">
                            <span>{{ props.row.name }}</span>
                        </el-form-item>
                        <el-form-item label="队长姓名">
                            <span>{{ props.row.captainName }}</span>
                        </el-form-item>
                        <el-form-item label="队伍邮箱">
                            <span>{{ props.row.email }}</span>
                        </el-form-item>
                        <el-form-item label="领队电话">
                            <span>{{ props.row.captainPhone }}</span>
                        </el-form-item>
                        <el-form-item label="小组成员">
                            <div style="margin-left: 90px">
                                <div v-for="item in props.row.teammates" :key="item.id">
                                    <span class="title">姓名：</span>{{ item.name }}
                                    <span class="title">学号：</span>{{item.studentNo}}
                                    <span class="title">学院：</span>{{item.school}}
                                    <span class="title">电话：</span>{{item.phoneNo}}
                                    <span class="title">邮箱：</span>{{item.email}}
                                </div>
                            </div>
                        </el-form-item>

                    </el-form>
                </template>
            </el-table-column>

            <el-table-column
                    type="index"
                    width="50"
                    align="center"
                    label="序号">
            </el-table-column>

            <el-table-column
                    prop="name"
                    label="队伍名称"
                    align="center"
                    show-overflow-tooltip>
            </el-table-column>

            <el-table-column
                    prop="captainName"
                    label="队长姓名"
                    width="100"
                    align="center"
                    show-overflow-tooltip>
            </el-table-column>

            <el-table-column
                    prop="email"
                    label="队伍邮箱"
                    width="250"
                    align="center"
                    show-overflow-tooltip>
            </el-table-column>

            <el-table-column
                    prop="captainPhone"
                    label="领队电话"
                    width="200"
                    align="center"
                    show-overflow-tooltip>
            </el-table-column>

            <el-table-column
                    prop="expireAt"
                    label="晋级"
                    width="100"
                    align="center"
                    show-overflow-tooltip>
                <template slot-scope="scope">
                    <span>{{isPromotion(scope.row)}}</span>
                </template>
            </el-table-column>

            <el-table-column
                    prop="file"
                    label="上交"
                    width="120"
                    align="center">
            </el-table-column>

        </el-table>
        <hr class="dif7">
        <div class="div-30"></div>

            <div class="dif5">
<!--                <el-button size="small">批量删除</el-button>-->
                <el-button size="small" type="primary" round @click="changeOutStatus" class="dif6">淘汰队伍</el-button>
                <el-button size="small" type="primary" round @click="downloadGroupInfo">下载信息</el-button>
                <el-button size="small" type="primary" round @click="sendNotice">发送通知</el-button>
                <el-button size="small" type="primary" round @click="downloadFile">文件下载</el-button>
                <el-button size="small" type="primary" round @click="tRelated">任务分派/取消</el-button>
<!--                <el-button size="small">成绩添加</el-button>-->
            </div>


        <out-status :visible="outStatusVisible"
                    :dialogClose.sync="outStatusVisible"
                    :stage-value="stageValue"
                    @success="getCompetitionGroups(yearValue)"></out-status>
        <edit-group-info :visible="editGroupInfoVisible"
                         :dialogClose.sync="editGroupInfoVisible"
                         :groupInfo="groupInfo"></edit-group-info>
        <send-message :visible="sendMessageVisible"
                      :dialogClose.sync="sendMessageVisible"></send-message>
        <download-files :visible="downloadFilesVisible"
                        :dialogClose.sync="downloadFilesVisible"
                        :stage="stageValue"></download-files>
    </div>

</template>

<script>
    import {
        getTeachersInfo,
        getAdminCompetition,
        getCompetitionGroups,
        getStageFile,
        downloadGroupInfo,
        toggleRelated,
        relatedT,
        yearCompetitionId} from "@/api/adminConsole";
    import {getGroupFiles} from '@/api/userConsole';
    import sendMessage from "@/views/adminConsole/components/sendMessage";
    import downloadFiles from "@/views/adminConsole/components/downloadFiles";
    import editGroupInfo from "@/views/adminConsole/components/editGroupInfo";
    import outStatus from "@/views/adminConsole/components/outStatus";
    import sortValue from "@/utils/sort";

    export default {
        name: "teamManagement",
        components:{sendMessage,downloadFiles,editGroupInfo,outStatus},
        data() {
            return {
                //比赛阶段选项
                stageOptions: [],
                //比赛阶段选择值
                stageValue: '',
                //比赛年份选项
                competitionYearOptions: [],
                //比赛年份
                yearValue:'',
                //关键词选项
                searchOptions: [
                    { value: 'name', label: '队伍名称' },
                    { value: 'captainName', label: '队长姓名' },
                    { value: 'email', label: '队伍邮箱' },
                    { value: 'captainPhone', label: '领队电话' },
                    { value: 'teammateName', label: '组员姓名' },
                ],
                //关键词
                searchKeyValue: 'name',
                //管理员维护的比赛列表
                AdminCompetition:[],
                //表单参数
                tableData: [],
                groupInfo: {},//编辑框传参
                editGroupInfoVisible:false, //编辑对话框
                sendMessageVisible:false,//发送通知对话框
                downloadFilesVisible:false,//下载文件对话框
                outStatusVisible:false,//晋级选项框
                chosenGroups:[],//左侧多选数组
                search:'',//搜索参数
                competitionId:'',//比赛id
                selectJudges:false,
                getTeachers: [],
                getTeacherIds:[],
                selectedTeacherId: '',
                selectedTeacherName:'',
                stageId: '',
                fileIds:[],
                tnames:''
            };
        },
        methods: {
            //编辑
            handleEdit(groupInfo) {
                this.editGroupInfoVisible = true;
                this.groupInfo = groupInfo;
            },
            //比赛年发生变化
            handleYearChange(year) {
                this.getCompetitionGroups(year);
                this.getCompetitionStage(year);
                this.getCompetitionId(year);
            },
            //晋级信息显示
            isPromotion(row) {
                if (Object.prototype.hasOwnProperty.call(row, 'expireAt')) {
                    if (row.expireAt <= this.stageValue) {
                        return '×';
                    } else if (row.expireAt > this.stageValue){
                        return '√';
                    }
                } else {
                    return '√';
                }
            },
            //获取比赛组
            getCompetitionGroups(year) {
                getCompetitionGroups(year).then( response => {
                    let tableData = response.data.data;

                    //获取队伍文件上交情况
                    getStageFile(this.stageValue).then( res => {
                        const signUpGroups = res.data.data;

                        for (const signUpGroup of signUpGroups) {
                            let index = -1;
                            for (let i=0; i<tableData.length; i++) {
                                if (tableData[i].id === signUpGroup.groupId) {
                                    index = i;
                                }
                            }
                            tableData[index].file = '√';
                        }
                        for (const group of tableData) {
                            if(!Object.prototype.hasOwnProperty.call(group, 'file')) {
                                group.file = '×';
                            }
                        }
                        this.tableData = tableData;
                    })
                })
            },
            //获取文件列表
            getFilesSignUp() {
                let tableData = this.tableData;
                getStageFile(this.stageValue).then( response => {
                    const signUpGroups = response.data.data;

                    if (signUpGroups.length === 0) {
                        for (const group of tableData) {
                                group.file = '×';
                        }
                    } else {
                        for (const signUpGroup of signUpGroups) {
                            for (const group of tableData) {
                                if(group.id === signUpGroup.groupId) {
                                    group.file = '√';
                                } else {
                                    group.file = '×';
                                }
                            }
                        }
                    }
                } )
            },
            //获取比赛年
            getCompetitionYear() {
                this.stageValue = '';//阶段值清空
                if (this.competitionYearOptions.length === 0) {
                    getAdminCompetition().then(response => {
                        let competitionList =  response.data.data;//管理员维护的比赛列表
                        sortValue(competitionList,'year')
                        sessionStorage.setItem('competitionList',JSON.stringify(competitionList));
                        for(let competition of competitionList) {
                            this.competitionYearOptions.push({
                                label: competition.year + '年',
                                value: competition.year
                            })
                        }
                    })
                }
            },
            //获取比赛年Id
            getCompetitionId(year) {
                yearCompetitionId(year).then( response => {
                    this.competitionId = response.data.data;
                })
            },
            //各小组的组别
            getType() {

            },

            //选择被派发任务教师
            getT()  {
                getTeachersInfo({staffId:this.selectedTeacherId,teacherName:this.selectedTeacherName}).then(res => {
                    this.getTeachers.push(res.data.data[0]);
                    this.getTeacherIds.push(res.data.data[0].teacherId)
                })
            },
            //将选中教师与competitionId关联
            cRelatedT(){
                let data = new FormData();
                data.append('teacherId',this.getTeachers[0].teacherId);
                data.append('competitionId',this.competitionId);
                relatedT(data).then( res => {
                    alert(res.data.data.msg);
                });
                this.selectJudges = false;
            },
            //将选中教师和fileId关联
            tRelated() {
                for (let group of this.chosenGroups){
                    this.tnames += (group.name+',');
                    getGroupFiles(group.id).then(async res => {
                        const info = res.data.data;
                        for (let i of info){
                            this.fileIds.push(i.fileId)
                        }
                    })
                }
                this.$confirm(`是否将小队${this.tnames.substring(0,this.tnames.length-1)}的项目文件派发/(取消派发）给已选择关联教师？`).then(async () => {
                    let dataz = {
                        fileId:this.fileIds,
                        teacherId:this.getTeacherIds
                    };
                    await toggleRelated(dataz).then(res => {
                        alert(res.data.data.msg);
                    });
                    this.fileIds = [];
                    this.tnames = '';
                });
            },

            //获取比赛阶段信息
            getCompetitionStage(year) {
                const competitionList =  JSON.parse(sessionStorage.getItem('competitionList'));
                for(const competition of competitionList) {
                    if(competition.year === year) {
                        let stages = competition.stages;
                        sortValue(stages, 'id');
                        this.stageOptions = [];
                        for(let i=0; i< stages.length; i++) {
                            this.stageOptions.push({
                                value: stages[i].id,
                                label: stages[i].name
                            })
                        }
                        this.stageValue = this.stageOptions[0].value;
                    }
                }
            },

            //发送通知
            sendNotice() {
                if(this.chosenGroups.length === 0) {
                    this.$message('请先选择比赛组！')
                } else {
                    this.sendMessageVisible = true;
                    this.$store.commit('sendNotice/SET_CHOSEN_GROUPS', this.chosenGroups)
                }
            },
            //下载文件
            downloadFile() {
                if (this.stageValue === '') {
                    this.$message('请先选择比赛与比赛阶段');
                    return false;
                }
                this.downloadFilesVisible = true;
            },
            //左侧多选选中事件
            handleChange(selection) {
                this.chosenGroups = selection;
            },
            //下载队伍信息
            downloadGroupInfo() {
                if (this.chosenGroups === []) {
                    this.$message('请先选择小组');
                    return false;
                }
                let groupList = [];
                for(const group of this.chosenGroups) {
                    groupList.push(group.id)
                }

                const data = {
                    cid: this.competitionId,
                    groupList: groupList
                };
                downloadGroupInfo(data).then( response => {
                    let fileName = '小组信息';// 文件名
                    let blob = new Blob([response.data], {
                        type:
                            "application/vnd.ms-excel,application/vnd.openxmlformats-officedocument.spreadsheetml.sheet"
                    });
                    if (window.navigator.msSaveOrOpenBlob) {
                        navigator.msSaveBlob(blob);
                    } else {
                        let elink = document.createElement("a");
                        elink.href = URL.createObjectURL(blob);
                        elink.download = fileName;
                        elink.style.display = "none";
                        document.body.appendChild(elink);
                        elink.click();
                        document.body.removeChild(elink);
                    }
                })
            },
            //改变淘汰状态
            changeOutStatus() {
                if(this.chosenGroups.length === 0) {
                    this.$message('请选择小组');
                    return false;
                }
                this.$store.commit('sendNotice/SET_CHOSEN_GROUPS', this.chosenGroups)
                this.outStatusVisible = true;
            },

            //搜索
            searchInfo() {
                if(this.searchKeyValue === 'name') {
                    return this.tableData.filter(data => !this.search || data.name.toLowerCase().includes(this.search.toLowerCase()));
                } else if (this.searchKeyValue === 'captainName') {
                    return this.tableData.filter(data => !this.search || data.captainName.toLowerCase().includes(this.search.toLowerCase()))
                } else if (this.searchKeyValue === 'email') {
                    return this.tableData.filter(data => !this.search || data.email.toLowerCase().includes(this.search.toLowerCase()))
                } else if (this.searchKeyValue === 'captainPhone') {
                    return this.tableData.filter(data => !this.search || data.captainPhone.toLowerCase().includes(this.search.toLowerCase()))
                } else if (this.searchKeyValue === 'teammateName') {
                    return this.tableData.filter(data => {
                        let flag = false;
                        for(const teammate of data.teammates) {
                            if(teammate.name.toLowerCase().includes(this.search.toLowerCase())) {
                                flag = true;
                            }
                        }
                        return !this.search || flag;
                    })
                }
            },
        },
        mounted() {
            this.getCompetitionYear();
            // this.handleYearChange(this.yearValue);

        }
    }
</script>

<style lang="scss" scoped>
    #teamManagement {
        background-color: #FFFFFF;
        margin: 0 auto;
        padding: 20px;

        /deep/.el-table thead {
            font-weight: bold;
            color: #344a5f;
        }

        .demo-table-expand {

            .title:not(:first-of-type) {
                margin-left: 2em;
            }

            /deep/.el-form-item__label {
                width: 90px;
                color: #99a9bf;
            }

            /deep/.el-form-item {
                margin-right: 0;
                margin-bottom: 0;
            }
        }
    }

    @media screen and (max-width: 420px){

        /deep/ .el-select.el-select--small{
            width: 40vw;
        }

        /deep/.el-table{
            margin-top:20vh;
        }

        .dif2{
            position: absolute;
            top: 15vh;
            right: 2vw;
        }

        .dif3{
            position: absolute;
            width: 60vw;
            top: 12vh;
            right: -8vw;
            height: 1px;
            color: #666666;
        }

        .dif4{
            position: absolute;
            width: 60vw;
            top: 14vh;
            right: -10vw;
            height: 1px;
            color: #cccccc;
        }

        .dif5{
            padding-top: 0;
            line-height: 6vh;
        }

        .dif6{
            margin-left: 2.2vw;
        }

        .dif7{
            position: absolute;
            width: 60vw;
            bottom: 38vh;
            right: 0;
            height: 1px;
            color: #cccccc;
        }

    }

    @media screen and (min-width: 421px){

        .dif3,.dif4,.dif7{
            display: none;
        }
    }


</style>
