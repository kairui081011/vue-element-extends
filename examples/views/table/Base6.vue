<template>
  <div v-loading="loading">
    <p style="color: red;font-size: 12px;">排序；服务端排序</p>

    <el-form ref="tableform" class="base-table6-form" size="mini" :inline="true" :model="formData">
      <el-form-item label="名字" prop="name">
        <el-input v-model="formData.name" placeholder="名字"></el-input>
      </el-form-item>
      <el-form-item label="角色" prop="role">
        <el-input v-model="formData.role" placeholder="请输入角色"></el-input>
      </el-form-item>
      <el-form-item>
        <el-button type="primary" @click="searchEvent">查询</el-button>
        <el-button @click="$refs.tableform.resetFields()">重置</el-button>
      </el-form-item>
    </el-form>

    <div class="base-table6-oper">
      <el-button type="success" size="mini" @click="exportCsvEvent">导出</el-button>
    </div>

    <elx-table
      ref="elxTable"
      border
      height="466"
      size="medium"
      :data.sync="list"
      @sort-change="sortChangeEvent"
      style="width: 100%">
      <elx-table-column type="selection" width="55"></elx-table-column>
      <elx-table-column prop="id" label="ID" width="80"></elx-table-column>
      <elx-table-column prop="name" label="名字" sortable show-overflow-tooltip></elx-table-column>
      <elx-table-column prop="age" label="年龄" sortable></elx-table-column>
      <elx-table-column prop="role" label="角色" sortable show-overflow-tooltip></elx-table-column>
      <elx-table-column prop="describe" label="文本域" show-overflow-tooltip></elx-table-column>
      <elx-table-column prop="date" label="日期" sortable :formatter="formatterDate"></elx-table-column>
      <elx-table-column prop="flag" label="是否启用"></elx-table-column>
      <elx-table-column prop="updateTime" label="更新时间" width="160" :formatter="formatterDate"></elx-table-column>
      <elx-table-column prop="createTime" label="创建时间" width="160" :formatter="formatterDate"></elx-table-column>
    </elx-table>

    <el-pagination
      class="base-table6-pagination"
      @size-change="handleSizeChange"
      @current-change="handleCurrentChange"
      :current-page="pageVO.currentPage"
      :page-sizes="[5, 10, 15, 20, 50, 100, 150, 200]"
      :page-size="pageVO.pageSize"
      layout="total, sizes, prev, pager, next, jumper"
      :total="pageVO.totalResult">
    </el-pagination>
  </div>
</template>

<script>
import XEUtils from 'xe-utils'
import XEAjax from 'xe-ajax'

export default {
  data () {
    return {
      loading: false,
      list: [],
      formData: {
        name: null,
        sex: null,
        role: null
      },
      pageVO: {
        currentPage: 1,
        pageSize: 10,
        totalResult: 0
      }
    }
  },
  created () {
    this.findList()
  },
  methods: {
    findList () {
      this.loading = true
      XEAjax.doGet(`/api/user/page/list/${this.pageVO.pageSize}/${this.pageVO.currentPage}`, this.formData).then(response => {
        let { page, result } = response.data
        this.list = result
        this.pageVO.totalResult = page.totalResult
        this.loading = false
      }).catch(e => {
        this.loading = false
      })
    },
    searchEvent () {
      this.pageVO.currentPage = 1
      this.findList()
    },
    handleSizeChange (pageSize) {
      this.pageVO.pageSize = pageSize
      this.findList()
    },
    handleCurrentChange (currentPage) {
      this.pageVO.currentPage = currentPage
      this.findList()
    },
    sortChangeEvent ({ column, prop, order }) {
      this.formData.order = order === 'ascending' ? 'asc' : 'desc'
      this.formData.sort = column.property
      this.searchEvent()
    },
    formatterDate (row, column, cellValue, index) {
      return XEUtils.toDateString(cellValue, 'yyyy-MM-dd HH:mm:ss')
    },
    exportCsvEvent () {
      this.$refs.elxTable.exportCsv()
    }
  }
}
</script>

<style>
.base-table6-oper {
  margin-bottom: 18px;
}
.base-table6-pagination {
  margin-top: 18px;
  text-align: right;
}
</style>
