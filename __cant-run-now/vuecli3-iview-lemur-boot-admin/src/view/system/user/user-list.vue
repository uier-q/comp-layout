<template>
  <div>
    <Card>
      <div class="search-con search-con-top">
        <Form :model="form" :label-width="80" inline>
          <FormItem label="用户">
            <Input v-model="form.name" placeholder="张三"></Input>
          </FormItem>
          <FormItem label="邮件">
            <Select v-model="form.email">
              <Option value="beijing">New York</Option>
              <Option value="shanghai">London</Option>
              <Option value="shenzhen">Sydney</Option>
            </Select>
          </FormItem>
          <FormItem label="创建时间">
            <Row>
              <Col span="11">
                <DatePicker type="date" placeholder="开始" v-model="form.map.crtTime_st"></DatePicker>
              </Col>
              <Col span="2" style="text-align: center">-</Col>
              <Col span="11">
                <DatePicker type="date" placeholder="结束" v-model="form.map.crtTime_ed"></DatePicker>
              </Col>
            </Row>
          </FormItem>
          <Button @click="handleSearch" class="search-btn" type="primary">
            <Icon type="search"/>
            搜索
          </Button>
        </Form>
        <div>
          <Button type="primary" icon="md-add" @click="handleCreate">新增</Button> &nbsp;&nbsp;
          <Button type="primary" icon="md-trash" @click="handleDelete">删除</Button>
        </div>
      </div>
      <tables ref="tables" v-model="tableData" :columns="columns" @on-search="handleSearch"
              @on-update="handleUpdate" @on-detail="handleDetail" @on-selection-change="selectionChange"/>
    </Card>
    <userInfo ref="userInfoRef" ></userInfo>
  </div>
</template>

<script>
import Tables from '_c/tables'
import { L, D } from '@/libs/api.request'
import userInfo from './user-info'
import { getIds } from '@/libs/util'

export default {
  name: 'tables_page',
  components: {
    Tables,
    userInfo
  },
  data () {
    return {
      columns: [
        { title: '', key: 'id', type: 'selection', width: 60 },
        { title: '账户', key: 'account' },
        { title: '名称', key: 'name' },
        { title: '手机', key: 'phone' },
        { title: '部门', key: 'deptId' },
        { title: '角色', key: 'roleid' },
        { title: '归属代理', key: 'tenantName' },
        { title: '是否管理', key: 'isAdmin' },
        { title: '创建时间', key: 'createTime', sortType: 'desc' },
        {
          title: '操作',
          key: 'handle',
          minWidth: 200,
          options: ['update', 'detail']
        }
      ],
      tableData: {
        rows: [],
        total: 0
      },
      form: {
        map: {}
      },
      selectedData: [],
      infoIsShow: false
    }
  },
  methods: {
    handleUpdate (param) {
      this.$refs.userInfoRef.openModel('update', param.row)
    },
    handleDetail (param) {
      this.$refs.userInfoRef.openModel('detail', param.row)
    },
    handleCreate () {
      this.$refs.userInfoRef.openModel('create')
    },
    handleDelete () {
      D('user', getIds(this.selectedData)).then(data => {
        this.$Message.success(data)
        this.handleSearch()
      })
    },
    selectionChange (selection) {
      this.selectedData = selection
    },
    exportExcel () {
      this.$refs.tables.exportCsv({
        filename: `table-${(new Date()).valueOf()}.csv`
      })
    },
    handleSearch (page, pageSize) {
      if (isNaN(page)) {
        page = 1
      }
      var param = {
        page: page,
        pageSize: pageSize,
        model: this.form,
        map: this.form.map
      }
      L('user', param).then(data => {
        this.tableData = data
      })
    }
  },
  mounted () {
    this.handleSearch()
  }
}
</script>

<style>

</style>
