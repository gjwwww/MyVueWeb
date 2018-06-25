<template>
  <div class="app-container">
    <div class="filter-container">
      <label style="color: #909399;font-weight: 500">XML名称：</label>
      <el-input @keyup.enter.native="handleFilter" style="width: 200px;" class="filter-item" :placeholder="$t('table.xmlName')" />
      <el-button class="filter-item" type="primary" v-waves icon="el-icon-search" >{{$t('table.search')}}</el-button>
      <el-button class="filter-item" style="margin-left: 10px;"  @click="tologin" type="primary" icon="el-icon-edit">{{$t('table.add')}}</el-button>
    </div>
    <el-table :key='tableKey' :data="list" v-loading="listLoading" element-loading-text="给我一点时间" border fit highlight-current-row
              style="width: 100%">
      <el-table-column align="center" :label="$t('table.id')" width="65">
        <template slot-scope="scope">
          <span>{{scope.row.id}}</span>
        </template>
      </el-table-column>
      <el-table-column with="150px" align="center" :label="$t('table.xmlName')">
        <template slot-scope="scope">
          <span>{{scope.row.roleName}}</span>
        </template>
      </el-table-column>
      <el-table-column with="150px" align="center" :label="$t('table.xmlAddress')">
        <template slot-scope="scope">
          <span>{{scope.row.xmlAddress}}</span>
        </template>
      </el-table-column>
      <el-table-column with="150px" align="center" :label="$t('table.lastOperator')">
        <template slot-scope="scope">
          <span>{{scope.row.lastOperator}}</span>
        </template>
      </el-table-column>
      <el-table-column with="150px" align="center" :label="$t('table.createTime')">
        <template slot-scope="scope">
          <span>{{scope.row.createTime}}</span>
        </template>
      </el-table-column>
      <el-table-column with="150px" align="center" :label="$t('table.modifyTime')">
        <template slot-scope="scope">
          <span>{{scope.row.modifyTime}}</span>
        </template>
      </el-table-column>
    </el-table>


    <div class="pagination-container">
      <el-pagination background @size-change="handleSizeChange" @current-change="handleCurrentChange" :current-page="listQuery.page" :page-sizes="[10,20,30, 50]" :page-size="listQuery.limit" layout="total, sizes, prev, pager, next, jumper" :total="total">
      </el-pagination>
    </div>
  </div>
</template>


<script>
  import waves from '@/directive/waves' // 水波纹指令
  import {BASE_API} from '../../../config/dev.env.js'

  export default {
    name: 'xmlFileTable',
    directives: {
      waves
    },
    data() {
      return {
        tableKey: 0,
        list: null,
        total: null,
        listLoading: true,
        listQuery: {
          page: 1,
          limit: 20,
          importance: undefined,
          title: undefined,
          type: undefined,
          sort: '+id'
        },
        showReviewer: false,
        temp: {
          id: undefined,
          rolename: '',
          name: '',
          rolevalue: '',
          timestamp: new Date()
        },
        dialogFormVisible: false,
        dialogStatus: '',
        textMap: {
          update: '编辑',
          create: '新增'
        },
        dialogPvVisible: false,
        pvData: [],
        rules: {
        },
        downloadLoading: false
      }
    },
    methods: {
      tologin() {
        let loginForm = {
          username: 'admin',
          password: '123456'
        }
        console.log('loginform：  ', loginForm)
        let config = {
          headers: {
            'Content-Type': 'Multipart/form-data'
          }
        }
        console.log('+++++++++++++++++++++', BASE_API)
        var url = BASE_API
        this.$http.post(url + '/user/login', loginForm, config, function(data) {
        })
      }
    }
  }
</script>
