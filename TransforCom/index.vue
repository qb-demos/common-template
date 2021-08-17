<template>
  <div class="wrap">
    <div class="tran-left">
      <el-input
        v-model="left.queryStr"
        placeholder="按名称搜索"
        clearable
        style="margin-bottom: 10px"
      ></el-input>
      <div class="title">
        <div>{{ leftTitle }}</div>
        <div>共 {{ left.totalData.length }} 条</div>
      </div>
      <el-table
        :data="left.showData"
        :height="tableHeight"
        style="width: 100%"
        border
        @selection-change="handleLeftSelectionChange"
      >
        <el-table-column type="selection" width="40" align="center">
        </el-table-column>
        <el-table-column
          prop="name"
          label="字段名称"
          header-align="center"
        ></el-table-column>
      </el-table>
      <div class="page-wrap">
        <!-- <el-pagination
          @current-change="handleLeftCurrentChange"
          :current-page.sync="pageLeft.current"
          :page-size="pageLeft.size"
          layout="total, prev, pager, next"
          :total="pageLeft.total"
        >
        </el-pagination> -->
      </div>
    </div>
    <div class="btn-wrap">
      <el-button
        type="primary"
        plain
        icon="el-icon-arrow-right"
        circle
        style="margin: 0 0 30px"
        @click="toRight"
      ></el-button>
      <el-button
        type="primary"
        plain
        icon="el-icon-arrow-left"
        circle
        style="margin: 0"
        @click="toLeft"
      ></el-button>
    </div>
    <div class="tran-right">
      <el-input
        v-model="right.queryStr"
        placeholder="按名称搜索"
        clearable
        style="margin-bottom: 10px"
      ></el-input>
      <div class="title">
        <div>{{ rightTitle }}</div>
        <div>共 {{ right.totalData.length }} 条</div>
      </div>
      <el-table
        :data="right.showData"
        :height="tableHeight"
        style="width: 100%"
        border
        @selection-change="handleRightSelectionChange"
      >
        <el-table-column type="selection" width="40" align="center">
        </el-table-column>
        <el-table-column
          prop="name"
          label="字段名称"
          header-align="center"
        ></el-table-column>
      </el-table>
    </div>
  </div>
</template>

<script>
export default {
  name: 'TransforCom',
  components: {},
  props: {
    leftData: {
      type: Array,
      default: () => []
    },
    rightData: {
      type: Array,
      default: () => []
    },
    // 本地搜索使用
    filterStr: {
      type: String,
      default: 'label'
    },
    // 主键，筛选列表使用
    primaryKey: {
      type: String,
      default: 'key'
    },
    leftTitle: {
      type: String,
      default: '待选列表'
    },
    rightTitle: {
      type: String,
      default: '已选列表'
    }
  },
  data () {
    return {
      tableHeight: 300,
      left: {
        queryStr: '',
        totalData: [],
        showData: [],
        selection: [],
        timer: null
      },
      right: {
        queryStr: '',
        totalData: [],
        showData: [],
        selection: [],
        timer: null
      },
      pageLeft: {
        current: 1,
        size: 20,
        total: 0
      }
    }
  },
  computed: {},
  watch: {
    leftData: {
      handler (n, o) {
        const arr = JSON.parse(JSON.stringify(n))
        this.$set(this.left, 'totalData', arr)
        this.filterLeft()
      },
      deep: true,
      immediate: true
    },
    rightData: {
      handler (n, o) {
        const arr = JSON.parse(JSON.stringify(n))
        this.$set(this.right, 'totalData', arr)
        this.filterRight()
      },
      deep: true,
      immediate: true
    },
    'left.queryStr': {
      handler (n, o) {
        if (n.trim() === '' && this.left.totalData.length === this.left.showData.length) {
          return
        }
        clearTimeout(this.left.timer)
        this.left.timer = setTimeout(() => {
          this.filterLeft()
        }, 300)
      },
      deep: true,
      immediate: true
    },
    'right.queryStr': {
      handler (n, o) {
        if (n.trim() === '' && this.right.totalData.length === this.right.showData.length) {
          return
        }
        clearTimeout(this.right.timer)
        this.right.timer = setTimeout(() => {
          this.filterRight()
        }, 300)
      },
      deep: true,
      immediate: true
    }
  },
  created () { },
  mounted () { },
  methods: {
    init () { },
    // 勾选
    handleLeftSelectionChange (val) {
      this.$set(this.left, 'selection', val)
    },
    handleRightSelectionChange (val) {
      this.$set(this.right, 'selection', val)
    },
    // 左侧翻页
    handleLeftCurrentChange (val) {
      this.pageLeft.current = val
    },
    filterLeft () {
      if (this.left.queryStr.trim() === '') {
        this.$set(this.left, 'showData', JSON.parse(JSON.stringify(this.left.totalData)))
      }
      const queryStr = this.left.queryStr
      const totalData = this.left.totalData
      const showData = totalData.filter(v => v[this.filterStr].includes(queryStr))
      this.$set(this.left, 'showData', showData)
    },
    filterRight () {
      if (this.right.queryStr.trim() === '') {
        this.$set(this.right, 'showData', JSON.parse(JSON.stringify(this.right.totalData)))
      }
      const queryStr = this.right.queryStr
      const totalData = this.right.totalData
      const showData = totalData.filter(v => v[this.filterStr].includes(queryStr))
      this.$set(this.right, 'showData', showData)
    },
    toRight () {
      if (this.left.selection.length === 0) {
        this.$message.warning('请在左侧勾选要移动的项！')
        return
      }
      // 添加到右侧
      const rightTotal = JSON.parse(JSON.stringify(this.right.totalData))
      rightTotal.unshift(...this.left.selection)
      this.$set(this.right, 'totalData', rightTotal)
      this.filterRight()

      // 从左侧删除
      const selKeys = this.left.selection.map(v => v[this.primaryKey])
      const leftTotal = this.left.totalData.filter(v => !selKeys.includes(v[this.primaryKey]))
      this.$set(this.left, 'totalData', leftTotal)
      this.filterLeft()

      this.emitData()
    },
    toLeft () {
      if (this.right.selection.length === 0) {
        this.$message.warning('请在右侧勾选要移动的项！')
        return
      }
      // 添加到左侧
      const leftTotal = JSON.parse(JSON.stringify(this.left.totalData))
      leftTotal.unshift(...this.right.selection)
      this.$set(this.left, 'totalData', leftTotal)
      this.filterLeft()

      // 从右侧删除
      const selKeys = this.right.selection.map(v => v[this.primaryKey])
      const rightTotal = this.right.totalData.filter(v => !selKeys.includes(v[this.primaryKey]))
      this.$set(this.right, 'totalData', rightTotal)
      this.filterRight()

      this.emitData()
    },
    // 数据发送到父组件
    emitData () {
      this.$emit('update:leftData', this.left.totalData)
      this.$emit('update:rightData', this.right.totalData)
    }
  }
}
</script>

<style lang='less' scoped>
.wrap {
  display: flex;
  justify-content: space-between;
  align-items: stretch;
  .tran-left {
    flex: 1;
  }
  .btn-wrap {
    display: flex;
    flex: 0 0 60px;
    flex-direction: column;
    justify-content: center;
    align-items: center;
  }
  .tran-right {
    flex: 1;
  }
  .title {
    box-sizing: border-box;
    width: 100%;
    padding: 10px 15px;
    border: 1px solid #ebeef5;
    border-bottom: none;
    display: flex;
    justify-content: space-between;
  }
}
</style>
