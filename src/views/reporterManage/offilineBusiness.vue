<template>
  <div class="app-container">
    <el-row>
      <el-col :span="24">
        <div class="grid-content bg-purple-dark"></div>
      </el-col>
    </el-row>
    <label style="color: #909399;font-weight: 500">统计方式：</label>
    <el-select clearable style="width: 130px" class="filter-item" v-model="listQuery.statisticalMethod"
               :placeholder="$t('table.statisticalMethod')">
      <el-option v-for="item in statisticalOptions" :key="item" :label="item" :value="item">
      </el-option>
    </el-select>
    <label style="color: #909399;font-weight: 500">统计时间：</label>
    <el-date-picker v-model="startdate" type="date" :placeholder="$t('table.dateStart')"></el-date-picker>
    <el-date-picker v-model="enddate" type="date" :placeholder="$t('table.dateEnd')"></el-date-picker>
    <label style="color: #909399;font-weight: 500">统计岗位：</label>
    <el-select clearable style="width: 130px" class="filter-item" v-model="listQuery.statisticalPosition"
               :placeholder="$t('table.statisticalPosition')">
      <el-option v-for="item in positionOptions" :key="item" :label="item" :value="item">
      </el-option>
    </el-select>
    <div style="margin-top: 15px;">
      <el-input placeholder="请输入内容" v-model="selectContent" class="input-with-select">
        <el-select v-model="selectMethod" slot="prepend" placeholder="请选择">
          <el-option label="Post" value="1"></el-option>
          <el-option label="Get" value="2"></el-option>
        </el-select>
        <el-button class="filter-item" slot="append" @click="handleCreate" icon="el-icon-edit">{{$t('table.add')}}
        </el-button>
      </el-input>
    </div>
    <el-tabs v-model="activeName" @tab-click="handleClick">
      <el-tab-pane label="Headers" name="headers">用户管理</el-tab-pane>
      <el-tab-pane label="body" name="body">配置管理</el-tab-pane>
    </el-tabs>
    <el-container>
      <el-header>
        <p1>This is test</p1>
        <p2>This is test2</p2>
      </el-header>
    </el-container>
  </div>
</template>
<script>
  import waves from '@/directive/waves' // 水波纹指令
  export default {
    name: 'offilineTable',
    directives: {
      waves
    },
    data() {
      return {
        startdate: '',
        enddate: '',
        tableKey: 0,
        list: null,
        total: null,
        listLoading: true,
        multipleSelection: [],
        statisticalOptions: ['日', '月'],
        positionOptions: ['城市经理', 'BDM', 'BD', '经销商'],
        listQuery: {
          page: 1,
          limit: 20,
          statisticalMethod: '',
          statisticalPosition: ''
        },
        showReviewer: false,
        temp: {
          id: undefined,
          fileName: '',
          filePath: '',
          createDate: '',
          modifyDate: ''
        },
        dialogFormVisible: false,
        dialogStatus: '',
        textMap: {
          update: '编辑',
          create: '新增'
        },
        dialogPvVisible: false,
        pvData: [],
        rules: {},
        downloadLoading: false,
        selectMethod: '',
        selectContent: '',
        activeName: 'headers'
      }
    }
  }
</script>
