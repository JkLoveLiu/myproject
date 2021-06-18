<template>
  <div class="templateWindow">
    <div class="windowContent">
      <!--头部按钮-->
      <div style="margin-top: 9px">
        <el-button type="primary" icon="el-icon-circle-plus-outline"
                   @click="manageDialogAddVisible = true;">增加
        </el-button>
        <!--  选择框  -->
        <el-select v-model="SelectValue"
                   @change="clientFilter"
                   placeholder="按条件筛选"
                   style="margin-left: 15px">
          <el-option-group
            v-for="group in options"
            :key="group.label"
            :label="group.label">
            <el-option
              v-for="item in group.options"
              :key="item.value"
              :label="item.label"
              :value="item.value">
            </el-option>
          </el-option-group>
        </el-select>
      </div>
      <!--表格内容-->
      <el-table
        ref="multipleTable"
        :data="manageTableData"
        tooltip-effect="dark"
        style="width: 100%;margin-top: 7px">
        <el-table-column
          fixed
          prop="id"
          label="ID"
          width="70">
        </el-table-column>
        <el-table-column
          fixed
          prop="name"
          label="姓名"
          width="90">
        </el-table-column>
        <el-table-column
          fixed
          prop="username"
          label="账号"
          width="120">
        </el-table-column>
        <el-table-column
          fixed
          prop="power"
          :formatter="managePowerCN"
          label="职位"
          width="120">
        </el-table-column>
        <el-table-column
          fixed
          prop="statue"
          :formatter="manageStatueChinese"
          label="状态"
          width="100">
        </el-table-column>
        <el-table-column
          fixed
          label="操作"
          width="100">
          <template slot-scope="scope">
            <el-button type="text" size="small"
                       @click="manageDialogEditVisible = true;manageEditInfo(scope.row)">
              编辑
            </el-button>
            <el-button @click="manageHandleClick(scope.row)" type="text" size="small"
                       style="color: red!important;">
              删除
            </el-button>
          </template>
        </el-table-column>
      </el-table>
      <!--编辑按钮对话框-->
      <el-dialog title="编辑" :visible.sync="manageDialogEditVisible" width="35%">
        <el-form :model="manageForm" :rules="manageRules">
          <el-form-item label="姓名" :label-width="manageFormLabelWidth" prop="name">
            <el-input v-model="manageForm.name" autocomplete="off"></el-input>
          </el-form-item>
          <el-form-item label="账号" :label-width="manageFormLabelWidth" prop="username">
            <el-input v-model="manageForm.username" autocomplete="off"></el-input>
          </el-form-item>
          <el-form-item label="职位" :label-width="manageFormLabelWidth" prop="power">
            <el-select v-model="manageForm.power" placeholder="请选择职位">
              <el-option label="总经理" value="0"></el-option>
              <el-option label="经理" value="1"></el-option>
              <el-option label="游客" value="2"></el-option>
            </el-select>
          </el-form-item>
          <el-form-item label="状态" :label-width="manageFormLabelWidth">
            <el-select v-model="manageForm.statue" placeholder="请选择状态" prop="statue">
              <el-option label="正常" value="0"></el-option>
              <el-option label="禁用" value="1"></el-option>
            </el-select>
          </el-form-item>
        </el-form>
        <div slot="footer" class="dialog-footer">
          <el-button @click="manageDialogEditVisible = false">取 消</el-button>
          <el-button type="primary"
                     @click="manageDialogEditVisible = false;manageEditInfoTrue()">确 定
          </el-button>
        </div>
      </el-dialog>
      <!--增加按钮对话框-->
      <el-dialog title="增加" :visible.sync="manageDialogAddVisible" width="35%">
        <el-form :model="manageAddUserInfo" :rules="manageRules" ref="addUserInfo"
                 label-width="100px" class="demo-ruleForm">
          <el-form-item label="姓名" prop="name">
            <el-input v-model="manageAddUserInfo.name" placeholder="请输入姓名"></el-input>
          </el-form-item>
          <el-form-item label="账号" prop="username">
            <el-input v-model="manageAddUserInfo.username" placeholder="请输入账号"></el-input>
          </el-form-item>
          <el-form-item label="密码" prop="pwd">
            <el-input placeholder="请输入密码" v-model="manageAddUserInfo.pwd"
                      show-password></el-input>
          </el-form-item>
          <el-form-item label="职位" prop="power">
            <el-select v-model="manageAddUserInfo.power" placeholder="请选择职位">
              <el-option label="总经理" value="0"></el-option>
              <el-option label="经理" value="1"></el-option>
              <el-option label="游客" value="2"></el-option>
            </el-select>
          </el-form-item>
          <el-form-item label="状态" prop="statue">
            <el-select v-model="manageAddUserInfo.statue" placeholder="请选择状态">
              <el-option label="正常" value="0"></el-option>
              <el-option label="禁用" value="1"></el-option>
            </el-select>
          </el-form-item>
        </el-form>
        <div slot="footer" class="dialog-footer">
          <el-button @click="manageDialogAddVisible = false;manageResetForm('addUserInfo')">取消</el-button>
          <el-button type="primary" @click="manageSubmitForm('addUserInfo');">确 定</el-button>
        </div>
      </el-dialog>
      <!--分页按钮-->
      <div style="position: fixed;bottom: 35px;">
        <el-pagination
          small
          :page-size="managePageSize"
          :current-page.sync="manageCurrentPage"
          background
          @current-change="manageCurrentChange"
          layout="prev, pager, next"
          :total="manageTotalPage">
        </el-pagination>
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";

export default {
  name: "managerHtml",
  data() {
    return {
      // 选择框
      options: [
        {
          label: '所有用户',
          options: [{
            value: '1all',
            label: '所有数据'
          }]
        },
        {
          label: '职位',
          options: [{
            value: '0power',
            label: '总经理'
          }, {
            value: '1power',
            label: '经理'
          }, {
            value: '2power',
            label: '游客'
          }]
        },
        {
          label: '状态',
          options: [{
            value: '0statue',
            label: '正常'
          }, {
            value: '1statue',
            label: '禁用'
          }]
        }],
      // 是否经过筛选
      isFilter: false,
      // 过滤后的数据
      filterTableData: [],
      // 选择后的value
      SelectValue: '',
      // 规则
      manageRules: {
        name: [
          {required: true, message: '请输入姓名', trigger: 'blur'},
          {min: 2, max: 4, message: '长度在 2 到 4 个字符', trigger: 'blur'}
        ],
        username: [
          {required: true, message: '请输入账号', trigger: 'blur'},
          {min: 5, max: 11, message: '长度在 5 到 11 个字符', trigger: 'blur'}
        ],
        pwd: [
          {required: true, message: '请输入密码', trigger: 'blur'},
          {min: 6, max: 14, message: '长度在 6 到 14 个字符', trigger: 'blur'}
        ],
        power: [
          {required: true, message: '请选择职位', trigger: 'change'}
        ],
        statue: [
          {required: true, message: '请选择状态', trigger: 'change'}
        ]
      },
      manageIdx: 0,
      // 表格数据
      manageTableData: [],
      manageGetServerData: [],

      manageDialogEditVisible: false,
      manageDialogAddVisible: false,

      // 修改编辑框的内容
      manageForm: {
        id: '',
        name: '',
        username: '',
        power: '',
        statue: '',
        delivery: false,
        type: [],
      },
      manageFormLabelWidth: '100px',
      // 增加编辑框的数据
      manageAddUserInfo: {
        name: '',
        username: '',
        pwd: '',
        power: '',
        statue: '',
      },
      // 总条目数
      manageTotalPage: 56,
      // 每一页数目
      managePageSize: 8,
      // 当前页
      manageCurrentPage: 1,
    }
  },
  methods: {
    // 筛选的select
    clientFilter(value) {
      let num = value[0];
      let group = value.slice(1)
      this.manageCurrentPage = 1;
      let tab = this.manageGetServerData
      this.isFilter = true;
      switch (group) {
        case 'power':
          this.filterTableData = tab.filter(tab => tab.power === num);
          break;
        case 'statue':
          this.filterTableData = tab.filter(tab => tab.statue === num);
          break;
        default:
          this.filterTableData = this.manageGetServerData;
          this.isFilter = false;
          break;
      }
      this.manageTotalPage = this.filterTableData.length
      this.manageTableData = this.filterTableData.slice(0, 8)
    },
    // 数字转汉字
    manageStatueChinese(row, column, cellValue) {
      if (cellValue === '0') {
        return '正常'
      } else {
        return '禁用'
      }
    },
    managePowerCN(row, column, cellValue) {
      if (cellValue === '0') {
        return '总经理'
      } else if (cellValue === '1') {
        return '经理'
      } else {
        return '游客'
      }
    },
    // 删除按钮
    manageHandleClick(row) {
      // 删除数据库里的数据
      axios
        .get('./php/delete/delete_manager.php?id=' + row.id)
        .then((res) => {
          if (res.data === 11) {
            this.manageGetUserData()
            this.$message.success('删除成功');
            this.manageCurrentPage = 1;
          }else if(res.data === 99){
            this.$message.error('操作失败，不能操作admin账号');
          } else {
            this.$message.error('删除失败，不能操作当前账号');
          }

        })
        .catch(function (error) { // 请求失败处理
          console.log(error);
        });
    },
    // 编辑按钮
    manageEditInfo(rows) {
      this.manageForm.id = rows.id;
      this.manageForm.username = rows.username;
      this.manageForm.name = rows.name;
      this.manageForm.power = rows.power;
      this.manageForm.statue = rows.statue;
    },
    // 编辑确定按钮
    manageEditInfoTrue() {
      axios({
        url: './php/update/update_managerInfo.php',
        method: 'post',
        // 数据格式
        data: 'id=' + this.manageForm.id +
          '&username=' + this.manageForm.username +
          '&name=' + this.manageForm.name +
          '&power=' + this.manageForm.power +
          '&statue=' + this.manageForm.statue,
        // 修改请求头，防止后端接收不到数据
        headers: {
          'Content-Type': 'application/x-www-form-urlencoded'
        }
      }).then((res) => {
        if (res.data === 1) {
          this.$message.warning('不能操作当前账号');
        }else if(res.data === 99){
          this.$message.error('操作失败，不能操作admin账号');
        } else {
          this.manageGetUserData();
          this.$message.success('编辑用户数据成功');
        }
      }).catch(function (error) { // 请求失败处理
        console.log(error);
      });
    },
    // 添加用户
    manageAddUserInfoData() {
      axios({
        url: './php/insert/insert_UserName.php',
        method: 'post',
        // 数据格式
        data: 'username=' + this.manageAddUserInfo.username +
          '&pwd=' + this.manageAddUserInfo.pwd +
          '&name=' + this.manageAddUserInfo.name +
          '&power=' + this.manageAddUserInfo.power +
          '&statue=' + this.manageAddUserInfo.statue,
        // 修改请求头，防止后端接收不到数据
        headers: {
          'Content-Type': 'application/x-www-form-urlencoded'
        }
      }).then((res) => {
        if (res.data === 3) {
          this.$message.error('账号已存在');
        } else if (res.data === 2) {
          this.$message.success('添加用户数据成功');
          this.manageGetUserData();
        }
        // 清空数据
        this.manageAddUserInfo.username = ''
        this.manageAddUserInfo.pwd = ''
        this.manageAddUserInfo.name = ''
      }).catch(function (error) { // 请求失败处理
        console.log(error);
      });

    },
    // 表单验证
    manageSubmitForm(formName) {
      this.$refs[formName].validate((valid) => {
        if (valid) {
          // 成功
          this.manageDialogAddVisible = false;
          this.manageAddUserInfoData();
        } else {
          // 失败
          return false;
        }
      });
    },
    manageResetForm(formName) {
      this.$refs[formName].resetFields();
    },
    // 从服务器获取用户数据
    manageGetUserData() {
      axios
        .get('./php/select/select_UserInfo.php')
        .then((res) => {
          this.manageGetServerData = res.data;
          this.manageTableData = this.manageGetServerData.slice(0, 8);
          this.manageTotalPage = this.manageGetServerData.length
          console.log(this.manageGetServerData)
          console.log(this.manageTableData)
        })
        .catch(function (error) { // 请求失败处理
          console.log(error);
        });
    },
    // 改变页数
    manageCurrentChange(nowPage) {
      console.log('点击了' + nowPage)
      // 是否经过筛选
      if (this.isFilter) {
        // 经过筛选
        this.manageFilterFn(nowPage, this.filterTableData)
      } else {
        // 未经过筛选
        this.manageFilterFn(nowPage, this.manageGetServerData)
      }
    },
    // 筛选完数据分页
    manageFilterFn(page, dataSource) {
      if (page === 1) {
        this.manageTableData = dataSource.slice(0, 8);
      } else {
        let start = (page - 1) * 8
        let end = page * 8
        if (end > dataSource.length) {
          end = dataSource.length;
        }
        this.manageTableData = dataSource.slice(start, end);
      }
    },
  },
  created() {
    this.manageGetUserData();
  }
}
</script>

<style scoped>
.templateWindow {
  height: calc(100% - 40px);
  width: 100%;
  background-color: #D3DCE6;
  position: absolute;
  left: 0;
  top: 40px
}

.windowContent {
  position: absolute;
  left: 10px;
  top: 0;
  width: calc(100% - 10px);
  height: calc(100% - 10px);
}
</style>
