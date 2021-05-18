<template>
    <div>
        <div style="margin-top: 10px">
            <el-table
                    :data="opLogs"
                    stripe
                    border
                    v-loading="loading"
                    element-loading-text="正在加载..."
                    element-loading-spinner="el-icon-loading"
                    element-loading-background="rgba(0, 0, 0, 0.8)"
                    style="width: 100%">
                <el-table-column
                        type="selection"
                        width="55">
                </el-table-column>
                <el-table-column
                        prop="id"
                        fixed
                        align="left"
                        label="id"
                        width="100">
                </el-table-column>
                <el-table-column
                        prop="addDate"
                        label="日期"
                        align="left"
                        width="250">
                </el-table-column>
                <el-table-column
                        prop="operate"
                        label="操作"
                        align="left"
                        width="400">
                </el-table-column>
                <el-table-column
                        prop="hrId"
                        label="hrId"
                        align="left"
                        width="85">
                </el-table-column>
            </el-table>
        </div>
    </div>
</template>

<script>
    export default {
        name: "SysLog",
        data() {
            return {
                loading: false,
                page: 1,
                size: 10,
                opLogs: [],
                total: 0,
                opLog: {
                    id: "",
                    addDate: "",
                    operate: "",
                    hrId: ""
                }
            }
        },
        mounted() {
            this.initOpLog();
        },
        methods: {
            initOpLog() {
                this.loading = true;
                let url = '/system/basic/opLog/?page=' + this.page + '&size=' + this.size;
                this.getRequest(url).then(resp => {
                    this.loading = false;
                    if (resp) {
                        this.opLogs = resp.data;
                        this.total = resp.total;
                    }
                });
            }
        }
    }
</script>

<style scoped>

</style>