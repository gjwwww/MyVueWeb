<template>
  <div class="app-container">
    <div class="filter-container">
      <label style="color: #909399;font-weight: 500">XML名称：</label>
      <el-input @keyup.enter.native="handleFilter" style="width: 200px;" class="filter-item" :placeholder="$t('table.xmlName')" v-model="listQuery.fileName" />
      <el-button class="filter-item" type="primary" v-waves icon="el-icon-search" @click ="tosearch">{{$t('table.search')}}</el-button>
      <el-button class="filter-item" style="margin-left: 10px;"  @click="tologin" type="primary" icon="el-icon-edit">{{$t('table.importFile')}}</el-button>
      <el-button class="filter-item" style='margin-left: 10px;' type="warning"  @click="handleDownload" :loading="downloadLoading">{{$t('excel.export')}}</el-button>
      <el-button class="filter-item" :loading="loading" style="margin-left:16px;"  type="primary" @click="handleUpload">Browse</el-button>
    </div>
    <el-table :key='tableKey' :data="list" v-loading="listLoading" element-loading-text="给我一点时间" border fit highlight-current-row @selection-change="handleSelectionChange"
              ref="multipleTable"
              style="width: 100%">
      <el-table-column type="selection" align="center"></el-table-column>
      <el-table-column align="center" :label="$t('table.id')" width="65">
        <template slot-scope="scope">
          <span>{{(listQuery.page-1)*listQuery.limit+parseInt(scope.$index)+1}}</span>
        </template>
      </el-table-column>
      <el-table-column with="150px" align="center" :label="$t('table.xmlName')">
        <template slot-scope="scope">
          <span>{{scope.row.fileName}}</span>
        </template>
      </el-table-column>
      <el-table-column with="150px" align="center" :label="$t('table.xmlAddress')">
        <template slot-scope="scope">
          <span>{{scope.row.filePath}}</span>
        </template>
      </el-table-column>
      <el-table-column with="150px" align="center" :label="$t('table.createTime')">
        <template slot-scope="scope">
          <span>{{scope.row.createDate}}</span>
        </template>
      </el-table-column>
      <el-table-column with="150px" align="center" :label="$t('table.modifyTime')">
        <template slot-scope="scope">
          <span>{{scope.row.modifyDate}}</span>
        </template>
      </el-table-column>
      <el-table-column align="center" :label="$t('table.actions')" width="230" class-name="small-padding fixed-width">
        <template slot-scope="scope">
          <!--<el-button type="primary" size="mini" @click="handleUpdate(scope.row)">{{$t('table.edit')}}</el-button>-->
          <el-button  size="mini" type="danger" @click="handleDelete(scope.row)">{{$t('table.delete')}}
          </el-button>
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
        multipleSelection: [],
        listQuery: {
          page: 1,
          limit: 20,
          fileName: ''
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
        rules: {
        },
        downloadLoading: false
      }
    },
    created() {
      // this.$message({
      //   message: '打开页面请求接口',
      //   type: 'success'
      // })
      this.getXMLTableList()
    },
    methods: {
      handleSelectionChange(val) {
        this.multipleSelection = val
      },
      tologin() {
        let loginForm = {
          username: 'admin',
          password: '123456'
        }
        let config = {
          headers: {
            'Content-Type': 'Multipart/form-data'
          }
        }
        this.$http.post('/api/user/login', loginForm, config).then(function(res) {
          console.log(res.data.message)
          this.$message({
            message: res.data.message,
            type: 'error'
          })
        })
      },
      handleSizeChange(val) {
        this.listQuery.limit = val
        this.getXMLTableList()
      },
      handleCurrentChange(val) {
        this.listQuery.page = val
        this.getXMLTableList()
      },
      getXMLTableList() {
        this.$http.post('/api/xmlFile/getAll', this.listQuery).then(function(res) {
          this.list = res.data.body.list
          console.log(res.data.body.list)
          this.total = parseInt(res.data.body.total)
          console.log(this.total)
          this.listLoading = false
        })
      },
      tosearch() {
        this.listQuery.page = 1
        this.getXMLTableList()
      },
      handleDelete(row) {
        this.temp = Object.assign({}, row) // copy obj
        this.$http.post('/api/xmlFile/deleteFile', { id: parseInt(this.temp.id) }).then(function(res) {
          if (res.data.status === 0) {
            this.$message({
              message: res.data.message,
              type: 'error'
            })
          } else {
            this.$message({
              message: res.data.message,
              type: 'success'
            })
          }
          this.getXMLTableList()
        })
      },
      handleDownload() {
        if (this.multipleSelection.length) {
          this.downloadLoading = true
          import('@/vendor/Export2Excel').then(excel => {
            const tHeader = ['Id', 'Title', 'Author', 'Readings', 'Date']
            const filterVal = ['id', 'title', 'author', 'pageviews', 'display_time']
            const list = this.multipleSelection
            const data = this.formatJson(filterVal, list)
            excel.export_json_to_excel({
              header: tHeader,
              data,
              filename: this.filename
            })
            this.$refs.multipleTable.clearSelection()
            this.downloadLoading = false
          })
        } else {
          this.$message({
            message: 'Please select at least one item',
            type: 'warning'
          })
        }
      },
      formatJson(filterVal, jsonData) {
        return jsonData.map(v => filterVal.map(j => v[j]))
      }
    }
  }
</script>
