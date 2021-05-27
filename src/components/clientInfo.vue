<template>
  <div class="templateWindow">
    <div class="windowContent">
      <!--  按钮  -->
      <div style="margin-top: 9px">
        <el-button type="primary"
                   icon="el-icon-circle-plus-outline"
                   :disabled="clientInsertBtnDisabled"
                   @click="clientDialogFormVisible = true;clientInsertBtn()">增加
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
      <!--  表格数据  -->
      <el-table
        :data="clientTableData"
        border
        style="width: 100%;margin-top: 7px">
        <el-table-column
          fixed="left"
          prop="name"
          label="客户名称"
          width="140">
        </el-table-column>
        <el-table-column
          prop="type"
          label="客户分类"
          :formatter="clientType"
          width="100">
        </el-table-column>
        <el-table-column
          prop="statue"
          label="客户意向"
          :formatter="clientStatue"
          width="100">
        </el-table-column>
        <el-table-column
          prop="manager"
          label="客户经理"
          width="100">
        </el-table-column>
        <el-table-column
          prop="require"
          label="客户需求"
          width="200">
        </el-table-column>
        <el-table-column
          prop="phone"
          label="客户联系方式"
          width="120">
        </el-table-column>
        <el-table-column
          prop="datetime"
          label="创建时间"
          width="160">
        </el-table-column>
        <el-table-column
          prop="complete"
          label="完成状态"
          :formatter="clientComplete"
          width="100">
        </el-table-column>
        <el-table-column
          prop="endedittime"
          label="最后操作时间"
          width="160">
        </el-table-column>
        <el-table-column
          prop="call"
          label="客户称呼"
          width="100">
        </el-table-column>
        <el-table-column
          prop="address"
          label="客户地址"
          :formatter="clientAddress"
          width="160">
        </el-table-column>
        <el-table-column
          prop="industry"
          label="客户行业"
          :formatter="clientIndustry"
          width="100">
        </el-table-column>
        <el-table-column
          fixed="right"
          label="操作"
          width="130">
          <template slot-scope="scope">
            <el-button @click="clientSelectClick(scope.row)" type="text" size="small">查看</el-button>
            <el-button type="text" size="small" @click="clientEditInfo(scope.row)">编辑</el-button>
            <el-button type="text" size="small" @click="clientDeleteInfo(scope.row)" style="color: red!important;">删除
            </el-button>
          </template>
        </el-table-column>
      </el-table>
      <!--  增加的dialog对话框  -->
      <el-dialog title="增加客户信息" :visible.sync="clientDialogFormVisible">
        <el-form :model="clientForm" :inline=true :rules="clientRules">
          <el-form-item label="客户名称" :label-width="clientFormLabelWidth" prop="name">
            <el-input v-model="clientForm.name" autocomplete="off"></el-input>
          </el-form-item>
          <el-form-item label="客户分类" :label-width="clientFormLabelWidth" prop="formType">
            <el-select v-model="clientForm.formType" placeholder="请选择客户分类">
              <el-option label="企业客户" value="1"></el-option>
              <el-option label="个人客户" value="0"></el-option>
            </el-select>
          </el-form-item>
          <el-form-item label="客户意向" :label-width="clientFormLabelWidth" prop="statue">
            <el-select v-model="clientForm.statue" placeholder="请选择客户意向">
              <el-option label="潜在客户" value="1"></el-option>
              <el-option label="意向客户" value="2"></el-option>
              <el-option label="现有客户" value="3"></el-option>
              <el-option label="代理客户" value="4"></el-option>
              <el-option label="供应商" value="5"></el-option>
              <el-option label="无效客户" value="0"></el-option>
            </el-select>
          </el-form-item>
          <el-form-item label="客户经理" :label-width="clientFormLabelWidth" prop="manager">
            <el-input v-model="clientForm.manager" autocomplete="off"></el-input>
          </el-form-item>
          <el-form-item label="客户需求" :label-width="clientFormLabelWidth" prop="require">
            <el-input v-model="clientForm.require" autocomplete="off"></el-input>
          </el-form-item>
          <el-form-item label="客户联系方式" :label-width="clientFormLabelWidth" prop="phone">
            <el-input v-model="clientForm.phone" autocomplete="off"></el-input>
          </el-form-item>
          <el-form-item label="客户称呼" :label-width="clientFormLabelWidth" prop="callName">
            <el-input v-model="clientForm.callName" autocomplete="off"></el-input>
          </el-form-item>
          <el-form-item label="客户地区" :label-width="clientFormLabelWidth" prop="address">
            <el-select v-model="clientForm.address" placeholder="请选择客户地区">
              <el-option label="华北地区" value="1"></el-option>
              <el-option label="东北地区" value="2"></el-option>
              <el-option label="华东地区" value="3"></el-option>
              <el-option label="华中地区" value="4"></el-option>
              <el-option label="华南地区" value="5"></el-option>
              <el-option label="西南地区" value="6"></el-option>
              <el-option label="西北地区" value="7"></el-option>
              <el-option label="港澳台" value="0"></el-option>
            </el-select>
          </el-form-item>
          <el-form-item label="客户行业" :label-width="clientFormLabelWidth" prop="industry">
            <el-select v-model="clientForm.industry" placeholder="请选择客户行业">
              <el-option label="IT互联网" value="1"></el-option>
              <el-option label="金融保险" value="2"></el-option>
              <el-option label="房产建筑" value="3"></el-option>
              <el-option label="教育培训" value="4"></el-option>
              <el-option label="娱乐传媒" value="5"></el-option>
              <el-option label="物流邮政" value="6"></el-option>
              <el-option label="法律咨询" value="7"></el-option>
              <el-option label="其他行业" value="0"></el-option>
            </el-select>
          </el-form-item>
        </el-form>
        <div slot="footer" class="dialog-footer">
          <el-button @click="clientDialogFormVisible = false">取 消</el-button>
          <el-button type="primary" @click="ClientDialogFormVisible = false;clientAddInfo()">确 定</el-button>
        </div>
      </el-dialog>
      <!--  查看的dialog对话框  -->
      <el-dialog title="查看详细信息" :visible.sync="clientShowDialogTableVisible">
        <el-table :data="clientShowTableInfoData">
          <el-table-column property="name" label="客户名称" width="190"></el-table-column>
          <el-table-column property="type" label="客户分类" width="150" :formatter="clientType"></el-table-column>
          <el-table-column property="statue" label="客户意向" width="190" :formatter="clientStatue"></el-table-column>
          <el-table-column property="manager" label="客户经理" width="120"></el-table-column>
        </el-table>
        <el-table :data="clientShowTableInfoData">
          <el-table-column property="require" label="客户需求" width="190"></el-table-column>
          <el-table-column property="phone" label="客户联系方式" width="150"></el-table-column>
          <el-table-column property="datetime" label="创建时间" width="190"></el-table-column>
          <el-table-column property="complete" label="完成状态" width="120" :formatter="clientComplete"></el-table-column>
        </el-table>
        <el-table :data="clientShowTableInfoData">
          <el-table-column property="endedittime" label="最后操作时间" width="190"></el-table-column>
          <el-table-column property="call" label="客户称呼" width="150"></el-table-column>
          <el-table-column property="address" label="客户地区" width="190" :formatter="clientAddress"></el-table-column>
          <el-table-column property="industry" label="客户行业" width="120" :formatter="clientIndustry"></el-table-column>
        </el-table>
      </el-dialog>
      <!--  编辑的dialog对话框    -->
      <el-dialog title="编辑客户信息" :visible.sync="clientEditDialogFormVisible">
        <el-form :model="clientEditFormData" :inline=true :rules="clientRules">
          <el-form-item label="客户名称" :label-width="clientFormLabelWidth" prop="name">
            <el-input v-model="clientEditFormData.name" autocomplete="off"></el-input>
          </el-form-item>
          <el-form-item label="客户分类" :label-width="clientFormLabelWidth" prop="formType">
            <el-select v-model="clientEditFormData.type" placeholder="请选择客户分类">
              <el-option label="个人客户" value="0"></el-option>
              <el-option label="企业客户" value="1"></el-option>
            </el-select>
          </el-form-item>
          <el-form-item label="客户意向" :label-width="clientFormLabelWidth" prop="statue">
            <el-select v-model="clientEditFormData.statue" placeholder="请选择客户意向">
              <el-option label="潜在客户" value="1"></el-option>
              <el-option label="意向客户" value="2"></el-option>
              <el-option label="现有客户" value="3"></el-option>
              <el-option label="代理客户" value="4"></el-option>
              <el-option label="供应商" value="5"></el-option>
              <el-option label="无效客户" value="0"></el-option>
            </el-select>
          </el-form-item>
          <el-form-item label="客户经理" :label-width="clientFormLabelWidth" prop="manager">
            <el-input v-model="clientEditFormData.manager" autocomplete="off"></el-input>
          </el-form-item>
          <el-form-item label="客户需求" :label-width="clientFormLabelWidth" prop="require">
            <el-input type="textarea" :rows="2" placeholder="请输入内容"
                      autocomplete="off"
                      v-model="clientEditFormData.require">
            </el-input>
          </el-form-item>
          <el-form-item label="客户联系方式" :label-width="clientFormLabelWidth" prop="phone">
            <el-input v-model="clientEditFormData.phone" autocomplete="off"></el-input>
          </el-form-item>
          <el-form-item label="完成状态" :label-width="clientFormLabelWidth" prop="formType">
            <el-select v-model="clientEditFormData.complete" placeholder="请选择完成状态">
              <el-option label="完成" value="0"></el-option>
              <el-option label="未完成" value="1"></el-option>
            </el-select>
          </el-form-item>
          <el-form-item label="客户称呼" :label-width="clientFormLabelWidth" prop="callName">
            <el-input v-model="clientEditFormData.call" autocomplete="off"></el-input>
          </el-form-item>
          <el-form-item label="客户地区" :label-width="clientFormLabelWidth" prop="address">
            <el-select v-model="clientEditFormData.address" placeholder="请选择客户地区">
              <el-option label="华北地区" value="1"></el-option>
              <el-option label="东北地区" value="2"></el-option>
              <el-option label="华东地区" value="3"></el-option>
              <el-option label="华中地区" value="4"></el-option>
              <el-option label="华南地区" value="5"></el-option>
              <el-option label="西南地区" value="6"></el-option>
              <el-option label="西北地区" value="7"></el-option>
              <el-option label="港澳台" value="0"></el-option>
            </el-select>
          </el-form-item>
          <el-form-item label="客户行业" :label-width="clientFormLabelWidth" prop="industry">
            <el-select v-model="clientEditFormData.industry" placeholder="请选择客户行业">
              <el-option label="IT互联网" value="1"></el-option>
              <el-option label="金融保险" value="2"></el-option>
              <el-option label="房产建筑" value="3"></el-option>
              <el-option label="教育培训" value="4"></el-option>
              <el-option label="娱乐传媒" value="5"></el-option>
              <el-option label="物流邮政" value="6"></el-option>
              <el-option label="法律咨询" value="7"></el-option>
              <el-option label="其他行业" value="0"></el-option>
            </el-select>
          </el-form-item>
        </el-form>
        <div slot="footer" class="dialog-footer">
          <el-button @click="clientEditDialogFormVisible = false;">取 消</el-button>
          <el-button type="primary" @click="clientEditDialogFormVisible = false;clientEditDialogTrue()">确 定</el-button>
        </div>
      </el-dialog>
      <!--  分页按钮   -->
      <div style="position: fixed;bottom: 35px;">
        <el-pagination
          :page-size="clientPageSize"
          :total="clientTotalPage"
          :current-page.sync="clientCurrentPage"
          background
          @current-change="clientCurrentChange"
          layout="prev, pager, next">
        </el-pagination>
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";

export default {
  name: "clientInfo",
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
          label: '客户分类',
          options: [{
            value: '1type',
            label: '企业客户'
          }, {
            value: '0type',
            label: '个人客户'
          }]
        },
        {
          label: '完成状态',
          options: [{
            value: '1complete',
            label: '未完成'
          }, {
            value: '0complete',
            label: '完成'
          }]
        },
        {
          label: '客户意向',
          options: [{
            value: '1statue',
            label: '潜在客户'
          }, {
            value: '2statue',
            label: '意向客户'
          }, {
            value: '3statue',
            label: '现有客户'
          }, {
            value: '4statue',
            label: '代理客户'
          }, {
            value: '5statue',
            label: '供应商'
          }, {
            value: '0statue',
            label: '无效客户'
          }]
        },
        {
          label: '客户地区',
          options: [{
            value: '1address',
            label: '华北地区'
          }, {
            value: '2address',
            label: '东北地区'
          }, {
            value: '3address',
            label: '华东地区'
          }, {
            value: '4address',
            label: '华中地区'
          }, {
            value: '5address',
            label: '华南地区'
          }, {
            value: '6address',
            label: '西南地区'
          }, {
            value: '7address',
            label: '西北地区'
          }, {
            value: '8address',
            label: '港澳台'
          }]
        },
        {
          label: '客户行业',
          options: [{
            value: '1industry',
            label: 'IT互联网'
          }, {
            value: '2industry',
            label: '金融保险'
          }, {
            value: '3industry',
            label: '房产建筑'
          }, {
            value: '4industry',
            label: '教育培训'
          }, {
            value: '5industry',
            label: '娱乐传媒'
          }, {
            value: '6industry',
            label: '医疗健康'
          }, {
            value: '7industry',
            label: '法律咨询'
          }, {
            value: '8industry',
            label: '其他行业'
          }]
        }],
      // 是否经过筛选
      isFilter: false,
      // 过滤后的数据
      filterTableData: [],
      // 选择后的value
      SelectValue: '',
      // 生成table所用的全部数据
      clientTableData: [],
      // 从服务获取的全部数据
      clientGetServerData: [],
      // 当前页数
      clientCurrentPage: 1,
      // 总条目数
      clientTotalPage: 56,
      // 每一页数目
      clientPageSize: 7,
      // 增加按钮是否禁用
      clientInsertBtnDisabled:true,
      // 添加的对话框
      clientDialogTableVisible: false,
      clientDialogFormVisible: false,
      // 查看的对话框
      clientShowDialogTableVisible: false,
      // 表格数据
      clientShowTableInfoData: [],
      // 增加的对话框
      clientForm: {
        name: '',
        formType: '',
        statue: '',
        manager: '',
        require: '',
        phone: '',
        address: '',
        callName: '',
        industry: '',
        complete: '',
      },
      clientFormLabelWidth: '120px',
      // 表单验证规则
      clientRules: {
        name: [
          {required: true, message: '请输入客户名称', trigger: 'blur'},
          {min: 2, max: 15, message: '长度在 2 到 15 个字符', trigger: 'blur'}
        ],
        formType: [
          {required: true, message: '请选择客户分类', trigger: 'change'}
        ],
        statue: [
          {required: true, message: '请选择客户状态', trigger: 'change'},
        ],
        manager: [
          {required: true, message: '请输入客户经理', trigger: 'blur'}
        ],
        require: [
          {required: true, message: '请输入客户需求', trigger: 'blur'},
          {min: 2, max: 20, message: '长度在 2 到 20 个字符', trigger: 'blur'}
        ],
        phone: [
          {required: true, message: '请输入客户联系方式', trigger: 'blur'},
        ],
        complete: [
          {required: true, message: '请选择完成状态', trigger: 'change'},
        ],
        address: [
          {required: true, message: '请输入客户地址', trigger: 'change'}
        ],
        callName: [
          {required: true, message: '请输入客户称呼', trigger: 'blur'},
          {min: 3, max: 4, message: '长度在 3 到 4 个字符', trigger: 'blur'}
        ],
        industry: [
          {required: true, message: '请输入客户行业', trigger: 'change'}
        ],
      },
      // 编辑对话框
      clientEditDialogFormVisible: false,
      // 编辑的表单
      clientEditFormData: {},
    }
  },
  methods: {
    // 筛选的select
    clientFilter(value) {
      let num = value[0];
      let group = value.slice(1)
      let tab = this.clientGetServerData
      // 跳转到第一页
      this.clientCurrentPage = 1;
      // 是否经过筛选
      this.isFilter = true;
      switch (group) {
        case 'type':
          this.filterTableData = tab.filter(tab => tab.type === num);
          break;
        case 'complete':
          this.filterTableData = tab.filter(tab => tab.complete === num);
          break;
        case 'statue':
          this.filterTableData = tab.filter(tab => tab.statue === num);
          break;
        case 'address':
          this.filterTableData = tab.filter(tab => tab.address === num);
          break;
        case 'industry':
          this.filterTableData = tab.filter(tab => tab.industry === num);
          break;
        default:
          this.filterTableData = this.clientGetServerData;
          this.isFilter = false;
          break;
      }
      this.clientTotalPage = this.filterTableData.length
      this.clientTableData = this.filterTableData.slice(0, 7)
    },
    // 客户行业
    clientIndustry(row, column, cellValue) {
      if (cellValue === '1') {
        return 'IT互联网'
      } else if (cellValue === '2') {
        return '金融保险'
      } else if (cellValue === '3') {
        return '房产建筑'
      } else if (cellValue === '4') {
        return '教育培训'
      } else if (cellValue === '5') {
        return '娱乐传媒'
      } else if (cellValue === '6') {
        return '医疗健康'
      } else if (cellValue === '7') {
        return '法律咨询'
      } else if (cellValue === '0') {
        return '其他行业'
      }
    },
    // 客户地区
    clientAddress(row, column, cellValue) {
      if (cellValue === '1') {
        return '华北地区'
      } else if (cellValue === '2') {
        return '东北地区'
      } else if (cellValue === '3') {
        return '华东地区'
      } else if (cellValue === '4') {
        return '华中地区'
      } else if (cellValue === '5') {
        return '华南地区'
      } else if (cellValue === '6') {
        return '西南地区'
      } else if (cellValue === '7') {
        return '西北地区'
      } else if (cellValue === '0') {
        return '港澳台'
      }
    },
    // 客户状态
    clientStatue(row, column, cellValue) {
      if (cellValue === '1') {
        return '潜在客户'
      } else if (cellValue === '2') {
        return '意向客户'
      } else if (cellValue === '3') {
        return '现有客户'
      } else if (cellValue === '4') {
        return '代理客户'
      } else if (cellValue === '5') {
        return '供应商'
      } else if (cellValue === '0') {
        return '无效客户'
      }
    },
    // 客户分类
    clientType(row, column, cellValue) {
      if (cellValue === '1') {
        return '企业客户'
      } else {
        return '个人客户'
      }
    },
    // 客户完成状态
    clientComplete(row, column, cellValue) {
      if (cellValue === '1') {
        return '未完成'
      } else {
        return '完成'
      }
    },
    // 改变页数
    clientCurrentChange(nowPage) {
      console.log('点击了' + nowPage)
      // 是否经过筛选
      if (this.isFilter) {
        // 经过筛选
        this.clientFilterFn(nowPage, this.filterTableData)
      } else {
        // 未经过筛选
        this.clientFilterFn(nowPage, this.clientGetServerData)
      }
    },
    // 筛选完数据分页
    clientFilterFn(page, dataSource) {
      if (page === 1) {
        this.clientTableData = dataSource.slice(0, 7);
      } else {
        let start = (page - 1) * 7
        let end = page * 7
        if (end > dataSource.length) {
          end = dataSource.length;
        }
        this.clientTableData = dataSource.slice(start, end);
      }
    },
    // 查看按钮
    clientSelectClick(row) {
      console.log(row);
      this.clientShowTableInfoData[0] = row;
      console.log(this.clientShowTableInfoData)
      this.clientShowDialogTableVisible = true
    },
    // 编辑
    clientEditInfo(row) {
      this.clientEditFormData = row;
      console.log(this.clientEditFormData)
      this.clientEditDialogFormVisible = true;
    },
    // 编辑客户信息确定按钮
    clientEditDialogTrue() {
      console.log(this.clientEditFormData)
      axios({
        url: './php/update/update_clientInfo.php',
        method: 'post',
        // 数据格式
        data: 'id=' + this.clientEditFormData.id +
          '&name=' + this.clientEditFormData.name +
          '&formType=' + this.clientEditFormData.type +
          '&statue=' + this.clientEditFormData.statue +
          '&manager=' + this.clientEditFormData.manager +
          '&require=' + this.clientEditFormData.require +
          '&phone=' + this.clientEditFormData.phone +
          '&address=' + this.clientEditFormData.address +
          '&callName=' + this.clientEditFormData.call +
          '&complete=' + this.clientEditFormData.complete +
          '&industry=' + this.clientEditFormData.industry,
        // 修改请求头，防止后端接收不到数据
        headers: {
          'Content-Type': 'application/x-www-form-urlencoded'
        }
      }).then((res) => {
        if (res.data === 2) {
          this.$message.success('修改成功')
        }
      }).catch(function (error) { // 请求失败处理
        console.log(error);
      });
      this.clientGetUserData()

    },
    // 删除
    clientDeleteInfo(row) {
      axios
        .get('./php/delete/delete_client.php?id=' + row.id)
        .then((res) => {
          if (res.data === 11) {
            this.$message.success('删除客户信息成功')
            this.clientGetUserData()
            this.clientCurrentPage = 1;
          } else {
            this.$message.error('删除客户信息失败')
          }
        })
        .catch(function (error) { // 请求失败处理
          console.log(error);
        });
    },
    // 获取所有客户信息
    clientGetUserData() {
      axios
        .get('./php/select/select_clientInfo.php')
        .then((res) => {
          this.clientGetServerData = res.data;
          this.clientTableData = this.clientGetServerData.slice(0, 7)
          this.clientTotalPage = this.clientGetServerData.length
        })
        .catch(function (error) { // 请求失败处理
          console.log(error);
        });
    },
    // 增加按钮
    clientInsertBtn() {
      for (let clientFormKey in this.clientForm) {
        console.log(clientFormKey)
        if (clientFormKey === 'formType' || clientFormKey === 'statue' ||
          clientFormKey === 'address' || clientFormKey === 'industry') {
          continue;
        }
        this.clientForm[clientFormKey] = '';
      }
    },
    // 增加确定按钮
    clientAddInfo() {
      this.clientDialogFormVisible = false;
      axios({
        url: './php/insert/insert_ClientInfo.php',
        method: 'post',
        // 数据格式
        data: 'name=' + this.clientForm.name +
          '&formType=' + this.clientForm.formType +
          '&statue=' + this.clientForm.statue +
          '&manager=' + this.clientForm.manager +
          '&require=' + this.clientForm.require +
          '&phone=' + this.clientForm.phone +
          '&address=' + this.clientForm.address +
          '&callName=' + this.clientForm.callName +
          '&industry=' + this.clientForm.industry,
        // 修改请求头，防止后端接收不到数据
        headers: {
          'Content-Type': 'application/x-www-form-urlencoded'
        }
      }).then((res) => {
        if (res.data === 2) {
          this.$message.success('用户数据增加成功')
          this.clientGetUserData()
        } else {
          this.$message.error('用户数据增加失败')
        }
      }).catch(function (error) { // 请求失败处理
        console.log(error);
      });
    }
  },
  created() {
    this.clientGetUserData()
    axios
      .get('./php/select/select_power.php')
      .then((res) => {
        if (res.data === 2){
          this.clientInsertBtnDisabled = false
        }
      })
      .catch(function (error) { // 请求失败处理
        console.log(error);
      });
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
