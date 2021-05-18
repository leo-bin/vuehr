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

        <el-table :data="emps" border stripe size="mini">
            <el-table-column type="selection" align="left" width="55"></el-table-column>
            <el-table-column prop="name" label="姓名" fixed width="150" align="left"></el-table-column>
            <el-table-column prop="workId" label="工号" width="150" align="left"></el-table-column>
            <el-table-column prop="gender" label="性别" width="150" align="left"></el-table-column>
            <el-table-column prop="baseSalary" label="基本工资" width="150" align="left"></el-table-column>
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
        </el-table>
        <div style="display: flex;justify-content: flex-end">
            <el-pagination
                    background
                    @current-change="currentChange"
                    @size-change="sizeChange"
                    layout="sizes, prev, pager, next, jumper, ->, total, slot"
                    :total="total">
            </el-pagination>
        </div>
    </div>
</template>

<script>
    export default {
        name: "SalSearch",
        data() {
            return {
                title: '',
                total: 0,
                page: 1,
                size: 10,
                loading: false,
                keyword: '',
                emps: [],
                emp: []
            }
        },
        mounted() {
            this.initEmps();
        },
        methods: {
            doSearch() {
                if (this.keyword) {
                    this.getRequest("/salary/sob/getByWorkId?workId=" + this.keyword).then(resp => {
                        if (resp.status === 200) {
                            this.emps = [];
                            // 数组赋值需要用push。。。
                            this.emps.push(resp.obj);
                        }
                    })
                }
            },
            initEmps() {
                this.loading = true;
                let url = '/salary/sob/getAll/?page=' + this.page + '&size=' + this.size;
                this.getRequest(url).then(resp => {
                    this.loading = false;
                    if (resp) {
                        this.emps = resp.data;
                        this.total = resp.total;
                    }
                });
            },
            sizeChange(currentSize) {
                this.size = currentSize;
                this.initEmps();
            },
            currentChange(currentPage) {
                this.page = currentPage;
                this.initEmps();
            },
        }
    }
</script>

<style scoped>

</style>