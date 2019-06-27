<template>
  <Card shadow class="role">
    <Table border :columns="columns" :data="roleList">
      <template slot-scope="{ row, index }" slot="action">
        <Button type="primary" size="small" style="margin-right: 5px" @click="rightAssign(row.role_id)">权限分配</Button>
      </template>
    </Table>
    <Drawer title="权限分配" width="700" :closable="false" v-model="showDrawer" class="role-drawer">
      <Card shadow>
        {{rightList}}
        <span slot="title">当前用户:{{user.realname}} 所属分组:{{user.deptName}}</span>
        <div v-for="(item, index) in rightList" :key="index" style="margin-bottom: 30px;">
          <div style="border-bottom: 1px solid #e9e9e9;padding-bottom:6px;margin-bottom:6px;">
            <Tooltip :disabled="item.right_desc == '' ? true : false" :content="item.right_desc" placement="bottom-start">
            <Checkbox
              :indeterminate="item.indeterminate"
              :value="item.check"
              @click.prevent.native="handleCheckAll(index)"
              >{{item.right_name}}</Checkbox>
            </Tooltip>
          </div>
          <CheckboxGroup v-model="item.activeList" @on-change="checkAllGroupChange(index)" v-if="item.list">
            <Checkbox :label="it.right_code" v-for= "(it, index) in item.list" :key="`check-${index}`">
              <Tooltip max-width="200" :content="it.right_desc" placement="bottom-start">
                {{it.right_name}}
              </Tooltip>
            </Checkbox>
          </CheckboxGroup>
        </div>
        <Button type="primary" @click="submit">提交</Button>
      </Card>
    </Drawer>
  </Card>
</template>

<script>
import { getrolelist, getrightlist } from '@/api/data'
import { mapState } from 'vuex'
export default {
  data () {
    return {
      showDrawer: false,
      roleList: [],
      rightList: [],
      selectedList: [],
      columns: [
        { type: 'index', title: '序号', width: 60 },
        { title: '角色名称', key: 'role_name' },
        {
          title: '当前状态',
          render: (h, params) => {
            return h('span', '正常')
          }
        },
        { title: '说明', key: 'role_desc' },
        {
          title: '操作',
          slot: 'action',
          align: 'center'
        }
      ],
      indeterminate: false,
      checkAll: false,
      checkAllGroup: []
    }
  },
  computed: {
    ...mapState(['user'])
  },
  created () {
    this.init()
  },
  methods: {
    init () {
      getrolelist().then(res => {
        if (res) {
          this.roleList = res.data.data
        }
      })
    },
    rightAssign (id) {
      this.showDrawer = true
      getrightlist(id).then(res => {
        if (res) {
          // this.selectedList = res.data.data.haved
          let rights = {
            '1': {
              'right_id': 1,
              'right_code': 'ALL',
              'right_name': '全部',
              'right_desc': '所有权限，超级管理员',
              'right_pid': 0
            },
            '2': {
              'right_id': 2,
              'right_code': 'USER',
              'right_name': '用户设置',
              'right_desc': '用户相关权限设置',
              'right_pid': 0,
              'list': [
                {
                  'right_id': 8,
                  'right_code': 'USER_VIEW',
                  'right_name': '查看用户',
                  'right_desc': '可以查看所属部门下的用户信息',
                  'right_pid': 2
                },
                {
                  'right_id': 9,
                  'right_code': 'USER_ADD',
                  'right_name': '添加用户信息',
                  'right_desc': '必须先具有查看权限，可以添加用户到所属部门',
                  'right_pid': 2
                },
                {
                  'right_id': 10,
                  'right_code': 'USER_EDIT',
                  'right_name': '编辑用户信息',
                  'right_desc': '必须先具有查看权限，可以编辑所属部门下的用户信息',
                  'right_pid': 2
                },
                {
                  'right_id': 11,
                  'right_code': 'USER_STAT',
                  'right_name': '启用/禁用用户',
                  'right_desc': '必须先具有查看权限，可以启用/禁用所属部门下的用户',
                  'right_pid': 2
                }
              ]
            },
            '3': {
              'right_id': 3,
              'right_code': 'RIGHT',
              'right_name': '权限设置',
              'right_desc': '',
              'right_pid': 0,
              'list': [
                {
                  'right_id': 12,
                  'right_code': 'RIGHT_VIEW',
                  'right_name': '权限查看',
                  'right_desc': '可以查看所属部门下的权限信息',
                  'right_pid': 3
                },
                {
                  'right_id': 13,
                  'right_code': 'RIGHT_EDIT',
                  'right_name': '权限更改',
                  'right_desc': '必须先具有查看权限，可以修改所属部门的权限信息',
                  'right_pid': 3
                }
              ]
            },
            '4': {
              'right_id': 4,
              'right_code': 'INSTRU',
              'right_name': '仪器管理',
              'right_desc': '',
              'right_pid': 0,
              'list': [
                {
                  'right_id': 14,
                  'right_code': 'INSTRU_VIEW',
                  'right_name': '仪器查看',
                  'right_desc': '可以查看所属部门下的仪器信息',
                  'right_pid': 4
                },
                {
                  'right_id': 15,
                  'right_code': 'INSTRU_EDIT',
                  'right_name': '仪器更改',
                  'right_desc': '必须先具有查看权限，可以修改所属部门的仪器信息',
                  'right_pid': 4
                }
              ]
            },
            '5': {
              'right_id': 5,
              'right_code': 'FINANCE',
              'right_name': '财务管理',
              'right_desc': '',
              'right_pid': 0,
              'list': [
                {
                  'right_id': 20,
                  'right_code': 'FINANCE_VIEW',
                  'right_name': '资产信息',
                  'right_desc': '可以查看所属部门下的资产信息',
                  'right_pid': 5
                },
                {
                  'right_id': 21,
                  'right_code': 'FINANCE_EDIT',
                  'right_name': '资产信息设置',
                  'right_desc': '必须先具有查看权限，设置或修改所属部门下的资产信息',
                  'right_pid': 5
                }
              ]
            },
            '6': {
              'right_id': 6,
              'right_code': 'REPORT',
              'right_name': '报表查看',
              'right_desc': '',
              'right_pid': 0,
              'list': [
                {
                  'right_id': 16,
                  'right_code': 'REPORT_VIEW',
                  'right_name': '报表查看',
                  'right_desc': '可以查看所属部门下的报表',
                  'right_pid': 6
                },
                {
                  'right_id': 17,
                  'right_code': 'REPORT_INSTRU',
                  'right_name': '仪器报表',
                  'right_desc': '可以查看所属部门下的仪器报表',
                  'right_pid': 6
                },
                {
                  'right_id': 18,
                  'right_code': 'REPORT_FINANCE',
                  'right_name': '财务报表',
                  'right_desc': '可以查看所属部门下的财务报表',
                  'right_pid': 6
                },
                {
                  'right_id': 19,
                  'right_code': 'REPORT_ANALYSIS',
                  'right_name': '分析报表',
                  'right_desc': '可以查看所属部门下的分析报表',
                  'right_pid': 6
                }
              ]
            },
            '7': {
              'right_id': 7,
              'right_code': 'SYSTEM',
              'right_name': '系统设置',
              'right_desc': '',
              'right_pid': 0,
              'list': [
                {
                  'right_id': 22,
                  'right_code': 'SYSTEM_PARAM',
                  'right_name': '系统参数设置',
                  'right_desc': '系统参数设置',
                  'right_pid': 7
                },
                {
                  'right_id': 23,
                  'right_code': 'SYSTEM_ROLE',
                  'right_name': '系统角色设置',
                  'right_desc': '系统角色设置，权限对应所属部门',
                  'right_pid': 7
                }
              ]
            }
          }
          this.rightList = Object.values(rights).map(item => {
            return Object.assign(item, { indeterminate: false, check: false, active: [], activeList: [], leng: item.list ? item.list.length : 0 }) // active为全选时的值or子选中的值 activeList子选中时的值
          })
        }
      })
    },
    handleCheckAll (index) {
      let result = this.rightList[index]
      if (result.indeterminate) {
        result.check = false
      } else {
        result.check = !result.check
      }
      result.indeterminate = false
      if (result.check) {
        if (result.list) {
          result.activeList = result.list.map(item => item.right_code)
        }
        result.active = []
        result.active.push(result.right_code)
      } else {
        result.active = []
        result.activeList = []
      }
    },
    checkAllGroupChange (index) {
      let result = this.rightList[index]
      if (result.activeList.length === result.leng) {
        result.indeterminate = false
        result.check = true
        result.active = []
        result.active.push(result.right_code)
      } else if (result.activeList.length > 0) {
        result.indeterminate = true
        result.check = false
        result.active = result.activeList
      } else {
        result.indeterminate = false
        result.check = false
        result.active = []
        result.activeList = []
      }
    },
    submit () {
      let arr = this.rightList.map(item => item.active).flat()
      let str = this.rightList[0].right_code
      if (arr.includes(str)) {
        arr = arr.splice(0, 1)
      }
      console.log(arr)
    }
  }
}
</script>

<style lang="less">
.role-drawer {
  .ivu-checkbox-group {
    padding-left: 15px;
  }
}
</style>
