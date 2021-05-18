<template>
    <div>
        <el-input placeholder="请输入员工号进行搜索，可以直接回车搜索..." prefix-icon="el-icon-search"
                  clearable
                  @clear="initEmps"
                  style="width: 350px;margin-right: 10px" v-model="keyword"
                  @keydown.enter.native="initEmps" :disabled="showAdvanceSearchView">
        </el-input>
        <el-button icon="el-icon-search" type="primary" @click="initEmps">
            搜索
        </el-button>

    </div>
</template>

<script>
    export default {
        name: "PerMv",
        data() {
            return {
                title: '',
                keyword: '',
                loading: false,
                dialogVisible: false,
                emp: {
                    name: "",
                    workID: "00000001",
                    gender: "男",
                    depName: "技术部"
                }
            }
        }
    }
</script>

<style scoped>

</style>