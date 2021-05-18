<template>
    <div>
        <el-input placeholder="请输入员工号进行搜索，可以直接回车搜索..." prefix-icon="el-icon-search"
                  clearable
                  style="width: 350px;margin-right: 10px" v-model="keyword"
        >
        </el-input>
        <el-button icon="el-icon-search" type="primary" @click="doSearch">
            搜索
        </el-button>

        <el-table :data="emp" border stripe size="mini">
            <el-table-column type="selection" align="left" width="55"></el-table-column>
            <el-table-column prop="name" label="姓名" fixed width="120" align="left"></el-table-column>
            <el-table-column prop="workId" label="工号" width="120" align="left"></el-table-column>
            <el-table-column prop="gender" label="性别" width="120" align="left"></el-table-column>
            <el-table-column prop="baseSalary" label="基本工资" width="120" align="left"></el-table-column>
            <el-table-column label="所属部门套账" align="center">
                <template slot-scope="scope">
                    <el-tooltip placement="right" v-if="scope.row.salary">
                        <div slot="content">
                            <table>
                                <tr>
                                    <td>基本工资</td>
                                    <td>{{scope.row.salary.basicSalary}}</td>
                                </tr>
                                <tr>
                                    <td>交通补助</td>
                                    <td>{{scope.row.salary.trafficSalary}}</td>
                                </tr>
                                <tr>
                                    <td>午餐补助</td>
                                    <td>{{scope.row.salary.lunchSalary}}</td>
                                </tr>
                                <tr>
                                    <td>奖金</td>
                                    <td>{{scope.row.salary.bonus}}</td>
                                </tr>
                                <tr>
                                    <td>养老金比率</td>
                                    <td>{{scope.row.salary.pensionPer}}</td>
                                </tr>
                                <tr>
                                    <td>养老金基数</td>
                                    <td>{{scope.row.salary.pensionBase}}</td>
                                </tr>
                                <tr>
                                    <td>医疗保险比率</td>
                                    <td>{{scope.row.salary.medicalPer}}</td>
                                </tr>
                                <tr>
                                    <td>医疗保险基数</td>
                                    <td>{{scope.row.salary.medicalBase}}</td>
                                </tr>
                                <tr>
                                    <td>公积金比率</td>
                                    <td>{{scope.row.salary.accumulationFundPer}}</td>
                                </tr>
                                <tr>
                                    <td>公积金基数</td>
                                    <td>{{scope.row.salary.accumulationFundBase}}</td>
                                </tr>
                                <tr>
                                    <td>启用时间</td>
                                    <td>{{scope.row.salary.createDate}}</td>
                                </tr>
                            </table>
                        </div>
                        <el-tag>{{scope.row.salary.name}}</el-tag>
                    </el-tooltip>
                    <el-tag v-else>暂未设置</el-tag>
                </template>
            </el-table-column>
            <el-table-column label="操作" align="center">
                <template slot-scope="scope">
                    <el-button @click="showEditEmpView(scope.row)" style="padding: 3px" size="mini">编辑</el-button>
                </template>
            </el-table-column>
        </el-table>

        <el-dialog
                :title="title"
                :visible.sync="dialogVisible"
                width="70%">
            <div>
                <el-form :model="editEmp" :rules="rules" ref="empEditForm">
                    <el-row>
                        <el-col :span="6">
                            <el-form-item label="员工基本薪资:" prop="afterSalary">
                                <el-input size="mini" style="width: 150px" prefix-icon="el-icon-edit"
                                          v-model="editEmp.afterSalary"
                                          placeholder="请输入员工薪资"></el-input>
                            </el-form-item>
                        </el-col>
                        <el-col :span="6">
                            <el-form-item label="调薪理由:" prop="reason">
                                <el-input size="mini" style="width: 200px" prefix-icon="el-icon-edit"
                                          v-model="editEmp.reason"
                                          placeholder="请输入调薪理由"></el-input>
                            </el-form-item>
                        </el-col>
                        <el-col :span="6">
                            <el-form-item label="调薪备注:" prop="remark">
                                <el-input size="mini" style="width: 200px" prefix-icon="el-icon-edit"
                                          v-model="editEmp.remark"
                                          placeholder="此次调薪备注"></el-input>
                            </el-form-item>
                        </el-col>
                    </el-row>
                </el-form>
            </div>
            <span slot="footer" class="dialog-footer">
    <el-button @click="dialogVisible = false">取 消</el-button>
    <el-button type="primary" @click="doUpdateEmpSal">确 定</el-button>
  </span>
        </el-dialog>
    </div>
</template>

<script>
    export default {
        name: "PerSalary",
        data() {
            return {
                title: '',
                keyword: '',
                emp: [],
                editEmp: {
                    eid: null,
                    beforeSalary: null,
                    afterSalary: null,
                    reason: "",
                    remark: ""
                },
                dialogVisible: false,
                rules: {
                    afterSalary: [{required: true, message: '请输入员工基本薪资', trigger: 'blur'}],
                    reason: [{required: true, message: '请输入调薪理由', trigger: 'blur'}],
                }
            }
        },
        methods: {
            doSearch() {
                if (this.keyword) {
                    this.getRequest("/salary/sob/getByWorkId?workId=" + this.keyword).then(resp => {
                        if (resp.status === 200) {
                            this.emp = [];
                            // 数组赋值需要用push。。。
                            this.emp.push(resp.obj);
                        }
                    })
                }
            },
            showEditEmpView(data) {
                this.editEmp = {};
                this.title = "编辑员工基本薪资";
                this.editEmp.eid = data.eid;
                this.editEmp.beforeSalary = data.baseSalary;
                this.dialogVisible = true;
            },
            doUpdateEmpSal() {
                this.$refs['empEditForm'].validate(valid => {
                    if (valid) {
                        this.postRequest("/salary/sob/adjustSal", this.editEmp).then(resp => {
                            if (resp.status === 200) {
                                this.dialogVisible = false;
                                // reload current data
                                this.doSearch();
                            }
                        })
                    }
                });
            }
        }
    }
</script>

<style scoped>

</style>