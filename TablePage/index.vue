<template>
  <div>
    <div class="route-content">
      <div class="filter-wrap">
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
        </div>
        <div>
          <el-button
            type="primary"
            icon="el-icon-circle-plus-outline"
            @click="add"
          >
            <span>新增</span>
          </el-button>
        </div>
      </div>

      <div>
        <el-table :data="tableData" :height="wrapHeight" border>
          <el-table-column
            v-for="col in cols"
            :prop="col.prop"
            :key="col.prop"
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
    </div>
  </div>
</template>

<script>
import * as request from '@/api/DataSource'
import { mapState } from 'vuex'

export default {
  name: 'DataSource',
  components: {},
  props: {},
  data () {
    return {
      tableData: [],
      cols: [],
      filter: {
        name: ''
      },
      options: {
        name: []
      },
      page: {
        size: 20,
        current: 1,
        total: 0
      }
    }
  },
  computed: {
    ...mapState({
      wrapHeight: (state) => state.app.wrapHeight - 110
    })
  },
  watch: {},
  created () {
    this.init()
  },
  mounted () { },
  methods: {
    init () {
      this.cols = [
        { label: '名称', prop: 'name' },
        { label: '描述', prop: 'desc' },
        { label: '状态', prop: 'status' }
      ]
    },
    getTableData () {
      const obj = {
        size: this.page.size,
        current: this.page.current,
        ...this.filter
      }
      request.envs(obj)
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

<style lang='scss' scoped>
</style>
