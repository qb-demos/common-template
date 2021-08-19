<template>
  <el-dialog
    :title="editType === 'edit' ? '编辑' : '新增'"
    :visible.sync="dialogVisible"
    width="800"
  >
    <div>content</div>
    <span slot="footer" class="dialog-footer">
      <el-button @click="dialogVisible = false">取 消</el-button>
      <el-button type="primary" @click="submit">确 定</el-button>
    </span>
  </el-dialog>
</template>

<script>
export default {
  name: 'EditCom',
  components: {},
  props: {},
  data () {
    return {
      editType: '',
      dialogVisible: false
    }
  },
  computed: {},
  watch: {},
  created () { },
  mounted () { },
  methods: {
    init (type) {
      this.editType = type
    },
    submit () {
      // todo
    }
  }
}
</script>

<style lang='less' scoped></style>
