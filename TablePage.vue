<template>
  <div>
    <div class="crumbs">
      <el-breadcrumb separator="/">
        <el-breadcrumb-item>
          <i class="el-icon-lx-cascades"></i>
          <span>&nbsp;{{ routeTitle }}</span>
        </el-breadcrumb-item>
      </el-breadcrumb>
    </div>

    <el-card class="box-card">
      <div class="filter-wrap">
        <div>
          <el-button type="primary" @click="add">添加</el-button>
        </div>
        <div>
          <el-select
            v-model="filter.name"
            placeholder="按名称筛选"
            clearable
            filterable
            @change="handleFilter"
          >
            <el-option
              v-for="item in options.name"
              :key="item.value"
              :label="item.label"
              :value="item.value"
            >
            </el-option>
          </el-select>
          <el-select
            v-model="filter.ip"
            placeholder="按IP筛选"
            clearable
            filterable
            @change="handleFilter"
          >
            <el-option
              v-for="item in options.ip"
              :key="item.value"
              :label="item.label"
              :value="item.value"
            >
            </el-option>
          </el-select>

          <el-button type="primary" plain @click="testAll">xxx</el-button>
        </div>
      </div>

      <div>
        <el-table :data="tableData" :height="tableHeight" border>
          <el-table-column
            v-for="col in cols"
            :prop="col.value"
            :key="col.value"
            :label="col.label"
            :width="col.width"
            :min-width="col['min-width']"
            align="center"
          >
          </el-table-column>
        </el-table>

        <div style="text-align: right; margin-top: 5px">
          <el-pagination
            @current-change="currentChange"
            :current-page.sync="page.current"
            :page-size="page.size"
            layout="total, prev, pager, next, jumper"
            :total="page.total"
          >
          </el-pagination>
        </div>
      </div>
    </el-card>
  </div>
</template>

<script>
import * as request from '@/api/HostsManage'

export default {
  name: 'HostsManage',
  components: {},
  props: {},
  data () {
    return {
      tableData: [],
      cols: [],
      filter: {
        name: '',
        ip: ''
      },
      options: {
        name: [],
        ip: []
      },
      page: {
        size: 20,
        current: 1,
        total: 0
      }
    }
  },
  computed: {
    routeTitle () {
      return this.$route.meta.title
    },
    tableHeight () {
      return this.$store.state.tableHeight - 178
    }
  },
  watch: {},
  created () {
    this.init()
  },
  mounted () { },
  methods: {
    init () {
      this.cols = [{
        label: 'IP',
        value: 'ip'
      }, {
        label: '名称',
        value: 'name'
      }, {
        label: '组',
        value: 'group'
      }, {
        label: '状态',
        value: 'status'
      }]
    },
    getTableData () {
      const obj = {
        size: this.page.size,
        current: this.page.current,
        ...this.filter
      }
      request.getTableData(obj)
        .then(res => {
          this.tableData = res.data.data
        })
        .catch(err => {
          Promise.reject(err)
        })
        .finally(() => {
          this.loading = false
        })
    },
    add () {
      // todo
    },
    testAll () {
      // todo
    },
    handleFilter () {
      this.page.current = 1
      this.getTableData()
    },
    currentChange (val) {
      this.page.current = val
      this.getTableData()
    }
  }
}
</script>

<style lang='less' scoped>
.filter-wrap {
  > div {
    &:last-child {
      > div {
        margin-right: 10px;
        &:last-child {
          margin-right: 0;
        }
      }
    }
  }
}
</style>
