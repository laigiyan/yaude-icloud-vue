<template>
  <a-card :bordered="false">
    <!-- 查詢區域 -->
    <div class="table-page-search-wrapper">
      <a-form layout="inline" @keyup.enter.native="searchQuery">
        <a-row :gutter="24">
          <a-col :xl="6" :lg="7" :md="8" :sm="24">
            <a-form-item label="實例名稱">
              <a-input placeholder="請輸入實例名稱" v-model="queryParam.instanceName"></a-input>
            </a-form-item>
          </a-col>
          <a-col :xl="6" :lg="7" :md="8" :sm="24">
            <a-form-item label="映像檔名稱">
              <a-input placeholder="請輸入映像檔名稱" v-model="queryParam.imgName"></a-input>
            </a-form-item>
          </a-col>
          <a-col :xl="6" :lg="7" :md="8" :sm="24">
            <span style="float: left;overflow: hidden;" class="table-page-search-submitButtons">
              <a-button type="primary" @click="searchQuery" icon="search">查詢</a-button>
              <a-button type="primary" @click="searchReset" icon="reload" style="margin-left: 8px">重置</a-button>
              <a @click="handleToggleSearch" style="margin-left: 8px">
                {{ toggleSearchStatus ? '收起' : '展開' }}
                <a-icon :type="toggleSearchStatus ? 'up' : 'down'"/>
              </a>
            </span>
          </a-col>
        </a-row>
      </a-form>
    </div>
    <!-- 查詢區域-END -->

    <!-- 操作按鈕區域 -->
    <div class="table-operator">
      <a-button @click="handleApply" type="primary" icon="plus">申請</a-button>
      <a-button type="primary"  @click="handlePowerOn" >開機</a-button>
      <a-button type="primary"  @click="handleShutDown" >關機</a-button>
      <a-button type="primary"  @click="handleReboot" >重啟</a-button>
      <a-button type="primary"  @click="handleShowConsole" >控制臺</a-button>
      <a-button type="primary"  @click="searchQuery" >刷新</a-button>



<!--      <a-button @click="handleAdd" type="primary" icon="plus">新增</a-button>-->
<!--      <a-button type="primary" icon="download" @click="handleExportXls('申請明細檔')">導出</a-button>-->
<!--      <a-upload name="file" :showUploadList="false" :multiple="false" :headers="tokenHeader" :action="importExcelUrl" @change="handleImportExcel">-->
<!--        <a-button type="primary" icon="import">導入</a-button>-->
<!--      </a-upload>-->
      <!-- 高級查詢區域 -->
<!--      <j-super-query :fieldList="superFieldList" ref="superQueryModal" @handleSuperQuery="handleSuperQuery"></j-super-query>-->
      <a-dropdown v-if="selectedRowKeys.length > 0">
        <a-menu slot="overlay">
          <a-menu-item key="1" @click="batchDel"><a-icon type="delete"/>刪除</a-menu-item>
        </a-menu>
        <a-button style="margin-left: 8px"> 批量操作 <a-icon type="down" /></a-button>
      </a-dropdown>
    </div>

    <!-- table區域-begin -->
    <div>
      <div class="ant-alert ant-alert-info" style="margin-bottom: 16px;">
        <i class="anticon anticon-info-circle ant-alert-icon"></i> 已選擇 <a style="font-weight: 600">{{ selectedRowKeys.length }}</a>項
        <a style="margin-left: 24px" @click="onClearSelected">清空</a>
      </div>

      <a-table
        ref="table"
        size="middle"
        :scroll="{x:true}"
        bordered
        rowKey="id"
        :columns="columns"
        :dataSource="dataSource"
        :pagination="ipagination"
        :loading="loading"
        :rowSelection="{selectedRowKeys: selectedRowKeys, onChange: onSelectChange}"
        class="j-table-force-nowrap"
        @change="handleTableChange">

        <template slot="htmlSlot" slot-scope="text">
          <div v-html="text"></div>
        </template>
        <template slot="imgSlot" slot-scope="text">
          <span v-if="!text" style="font-size: 12px;font-style: italic;">無圖片</span>
          <img v-else :src="getImgView(text)" height="25px" alt="" style="max-width:80px;font-size: 12px;font-style: italic;"/>
        </template>
        <template slot="fileSlot" slot-scope="text">
          <span v-if="!text" style="font-size: 12px;font-style: italic;">無文件</span>
          <a-button
            v-else
            :ghost="true"
            type="primary"
            icon="download"
            size="small"
            @click="downloadFile(text)">
            下載
          </a-button>
        </template>

        <span slot="instanceName" slot-scope="text, record">
          <a @click="handleRouter(record)">{{ text }}</a>
        </span>

        <span slot="action" slot-scope="text, record">
          <a @click="handleEdit(record)">編輯</a>

          <a-divider type="vertical" />
          <a-dropdown>
            <a class="ant-dropdown-link">更多 <a-icon type="down" /></a>
            <a-menu slot="overlay">
<!--              <a-menu-item>-->
<!--                <a @click="handleAdjustResource(record)">調整資源</a>-->
<!--              </a-menu-item>-->
              <a-menu-item>
                <a @click="handleShowMonitorById(record)">監控</a>
              </a-menu-item>
              <a-menu-item>
                <a @click="handleAddFloatingIP(record)">綁定浮動IP</a>
              </a-menu-item>
              <a-menu-item>
                <a @click="removeFloatingIP(record)">解除浮動IP的綁定</a>
              </a-menu-item>
              <a-menu-item>
                <a @click="handleCreateSnapshot(record)">快照</a>
              </a-menu-item>
              <a-menu-item>
                <a @click="handleShowConsoleRecord(record)">控制臺</a>
              </a-menu-item>
              <a-menu-item>
                <a @click="handleRebootBySOFT(record)">軟重啟實例</a>
              </a-menu-item>
              <a-menu-item>
                <a @click="handleRebootByHARD(record)">硬重啟實例</a>
              </a-menu-item>

              <a-menu-item>
                <a @click="handleConnectVolume(record)">連接卷</a>
              </a-menu-item>
              <a-menu-item>
                <a @click="handleSeparateVolume(record)">分離卷</a>
              </a-menu-item>
              <a-menu-item>
                  <a-popconfirm title="確定刪除嗎?" @confirm="() => handleDelete(record)">
                  <a>刪除</a>
                   </a-popconfirm>
              </a-menu-item>
            </a-menu>
<!--            <a-menu slot="overlay">-->
<!--              <a-menu-item>-->
<!--                <a @click="handleDetail(record)">詳情</a>-->
<!--              </a-menu-item>-->
<!--              <a-menu-item>-->
<!--                <a-popconfirm title="確定刪除嗎?" @confirm="() => handleDelete(record.id)">-->
<!--                  <a>刪除</a>-->
<!--                </a-popconfirm>-->
<!--              </a-menu-item>-->
<!--            </a-menu>-->

          </a-dropdown>
        </span>

      </a-table>
    </div>

    <os-instance-modal ref="modalForm" @ok="modalFormOk"></os-instance-modal>
    <os-available-volume ref="volumeForm"  @ok="modalFormOk"></os-available-volume>
    <os-in-use-volume ref="inUseVolumeForm" @ok="modalFormOk"></os-in-use-volume>
    <os-floating-ip ref="floatingIpForm"  @ok="modalFormOk"></os-floating-ip>
  </a-card>
</template>

<script>

  import '@/assets/less/TableExpand.less'
  import { mixinDevice } from '@/utils/mixin'
  import { JeecgListMixin } from '@/mixins/JeecgListMixin'
  import OsInstanceModal from './modules/OsInstanceModal'
  import { deleteAction, getAction,downFile,getFileAccessHttpUrl } from '@/api/manage'
  import OsAvailableVolume from './modules/OsAvailableVolume'
  import OsInUseVolume from './modules/OsInUseVolume'
  import OsFloatingIp from './modules/OsFloatingIp'

  export default {
    name: 'OsInstanceList',
    mixins:[JeecgListMixin, mixinDevice],
    components: {
      OsAvailableVolume,
      OsInstanceModal,
      OsInUseVolume,
      OsFloatingIp
    },
    data () {
      return {
        description: '申請明細檔管理頁面',
        // 表頭
        columns: [
          {
            title: '#',
            dataIndex: '',
            key:'rowIndex',
            width:60,
            align:"center",
            customRender:function (t,r,index) {
              return parseInt(index)+1;
            }
          },
          {
            title:'實例名稱',
            align:"center",
            dataIndex: 'instanceName',
            scopedSlots: { customRender: 'instanceName' }
          },
          {
            title:'狀態',
            align:"center",
            dataIndex: 'status'
          },
          {
            title:'映像檔名稱',
            align:"center",
            dataIndex: 'imgName'
          },
          {
            title:'虛擬硬體樣板',
            align:"center",
            dataIndex: 'flavorName'
          },
          {
            title:'實例配置',
            align:"center",
            dataIndex: 'configureInfo'
          },
          {
            title:'IP 位址',
            align:"center",
            dataIndex: 'ipAddress'
          },
          {
            title:'所屬專案',
            align:"center",
            dataIndex: 'projectName'
          },
          {
            title:'運行時長',
            align:"center",
            dataIndex: 'runTime'
          },
          {
            title: '操作',
            dataIndex: 'action',
            align:"center",
            fixed:"right",
            width:147,
            scopedSlots: { customRender: 'action' }
          }
        ],
        url: {
          list: "/openstack/osInstance/list",
          delete: "/openstack/osInstance/delete",
          deleteBatch: "/openstack/osInstance/deleteBatch",
          exportXlsUrl: "/openstack/osInstance/exportXls",
          importExcelUrl: "openstack/osInstance/importExcel",
          powerOnUrl: "/openstack/osInstance/powerOn",
          shutDownUrl: "/openstack/osInstance/shutDown",
          rebootByHARDUrl: "/openstack/osInstance/rebootByHARD",
          rebootBySOFTUrl: "/openstack/osInstance/rebootBySOFT",
          createSnapshotUrl: "/openstack/osInstance/createSnapshot"

        },
        dictOptions:{},
        superFieldList:[],
      }
    },
    created() {
    this.getSuperFieldList();
    },
    computed: {
      importExcelUrl: function(){
        return `${window._CONFIG['domianURL']}/${this.url.importExcelUrl}`;
      },
    },
    methods: {
      initDictConfig(){
      },
      getSuperFieldList(){
        let fieldList=[];
        fieldList.push({type:'string',value:'instanceName',text:'實例名稱',dictCode:''})
        fieldList.push({type:'string',value:'options',text:'申請狀態',dictCode:''})
        fieldList.push({type:'string',value:'status',text:'狀態',dictCode:''})
        fieldList.push({type:'string',value:'represent',text:'描述',dictCode:''})
        fieldList.push({type:'string',value:'imgId',text:'映像檔id',dictCode:''})
        fieldList.push({type:'string',value:'isDelete',text:'刪除實例時是否刪除卷',dictCode:''})
        fieldList.push({type:'string',value:'flavorId',text:'虛擬硬體樣板id',dictCode:''})
        fieldList.push({type:'string',value:'runStatus',text:'運行狀態',dictCode:''})
        fieldList.push({type:'string',value:'securityName',text:'安全群組',dictCode:''})
        fieldList.push({type:'string',value:'networkId',text:'網路',dictCode:''})
        this.superFieldList = fieldList
      },
      handleApply(){
        this.$router.push({name: 'os-OsApplyList',params:{}})
      },
      handlePowerOn(){
        if (this.selectionRows.length != 1){
          this.$message.error("請選擇一條記錄！")
          return
        }

        let that = this;
        this.$confirm({
          title: '確定開啟'+this.selectionRows[0].instanceName+'嗎?',
          //content: 'When clicked the OK button, this dialog will be closed after 1 second',
          onOk() {
            that.powerOn();
          },
          onCancel() {},
        });

      },
      powerOn(){
        let that = this;
        this.loading = true;
        getAction(that.url.powerOnUrl, this.selectionRows[0]).then((res) => {
          if (res.success) {
            that.$message.success(res.message);
            that.loadData();
            that.selectionRows = [];
            that.selectedRowKeys = [];
          } else {
            that.$message.warning(res.message);
          }
          this.loading = false;
        });
      },

      handleShutDown(){
        if (this.selectionRows.length != 1){
          this.$message.error("請選擇一條記錄！")
          return
        }

        let that = this;
        this.$confirm({
          title: '確定關閉'+this.selectionRows[0].instanceName+'嗎?',
          //content: 'When clicked the OK button, this dialog will be closed after 1 second',
          onOk() {
            that.shutDown();
          },
          onCancel() {},
        });
      },
      shutDown(){
        let that = this;
        this.loading = true;
        getAction(that.url.shutDownUrl, this.selectionRows[0]).then((res) => {
          if (res.success) {
            that.$message.success(res.message);
            that.loadData();
            that.selectionRows = [];
            that.selectedRowKeys = [];
          } else {
            that.$message.warning(res.message);
          }
          this.loading = false;
        });
      },

      handleReboot(){
        if (this.selectionRows.length != 1){
          this.$message.error("請選擇一條記錄！")
          return
        }

        let that = this;
        this.$confirm({
          title: '確定重啟'+this.selectionRows[0].instanceName+'嗎?',
          //content: 'When clicked the OK button, this dialog will be closed after 1 second',
          onOk() {
            that.reboot(that.selectionRows[0],that.url.rebootByHARDUrl);
          },
          onCancel() {},
        });
      },
      reboot(record,url){
        let that = this;
        this.loading = true;
        getAction(url, record).then((res) => {
          if (res.success) {
            that.$message.success(res.message);
            that.loadData();
            that.selectionRows = [];
            that.selectedRowKeys = [];
          } else {
            that.$message.warning(res.message);
          }
          this.loading = false;
        });
      },
      //軟重啟實例
      handleRebootBySOFT(record){
        let that = this;
        this.$confirm({
          title: '確定軟重啟'+record.instanceName+'嗎?',
          //content: 'When clicked the OK button, this dialog will be closed after 1 second',
          onOk() {
            that.reboot(record,that.url.rebootBySOFTUrl);
          },
          onCancel() {},
        });
      },
      //硬重啟實例
      handleRebootByHARD(record){
        let that = this;
        this.$confirm({
          title: '確定硬重啟'+record.instanceName+'嗎?',
          //content: 'When clicked the OK button, this dialog will be closed after 1 second',
          onOk() {
            that.reboot(record,that.url.rebootByHARDUrl);
          },
          onCancel() {},
        });
      },
      handleRouter(record){
        console.log('handleRoute  '+record)
        this.$router.push({name: 'instance-OsInstanceDetail',params:{id:record.id,projectId:record.projectId}})
      },
      //單實例監控
      handleShowMonitorById(record){
        //let that = this;
        this.$router.push({name: 'monitoring-InstanceById',params:{id:record.id,projectName:record.projectName,instanceName:record.instanceName}})
      },
      handleShowConsole(){
        if (this.selectionRows.length != 1){
          this.$message.error("請選擇一條記錄！")
          return
        }
        //let that = this;
        this.$router.push({name: 'instance-OsInstanceDetail',params:{id:this.selectionRows[0].id,projectId:this.selectionRows[0].projectId,selectKey:'2'}})
      },
      handleShowConsoleRecord(record){
        this.$router.push({name: 'instance-OsInstanceDetail',params:{id:record.id,projectId:record.projectId,selectKey:'2'}})
      },
      handleCreateSnapshot(record){
        let that = this;
        this.$confirm({
          title: '確定'+record.instanceName+'創建快照嗎?',
          //content: 'When clicked the OK button, this dialog will be closed after 1 second',
          onOk() {
            that.createSnapshot(record);
          },
          onCancel() {},
        });
      },
      createSnapshot(record){
        let that = this;
        this.loading = true;
        record.imgName = '快照'+record.id;
        getAction(this.url.createSnapshotUrl, record).then((res) => {
          if (res.success) {
            that.$message.success("快照創建成功");
            //that.loadData();
            that.selectionRows = [];
            that.selectedRowKeys = [];
          } else {
            that.$message.warning("快照創建失敗");
          }
          this.loading = false;
        });

      },
      handleDelete: function (record) {
        if(!this.url.delete){
          this.$message.error("請設置url.delete屬性!")
          return
        }
        var that = this;
        this.loading = true;
        deleteAction(that.url.delete, {id: record.id,projectId:record.projectId}).then((res) => {
          if (res.success) {
            //重新計算分頁問題
            that.reCalculatePage(1)
            that.$message.success(res.message);
            that.loadData();
          } else {
            that.$message.warning(res.message);
          }
          that.loading = false;
        });
      },
      //鏈接卷
      handleConnectVolume(record){
        this.$refs.volumeForm.edit(record);
      },
      //分離卷
      handleSeparateVolume(record){
        this.$refs.inUseVolumeForm.edit(record);
      },
      handleAddFloatingIP(record){
        record.floatingIpStatus = "DOWN";
        this.$refs.floatingIpForm.edit(record);
      },
      removeFloatingIP(record){
        record.floatingIpStatus = "ACTIVE";
        this.$refs.floatingIpForm.edit(record);
      }
    }
  }
</script>
<style scoped>
  @import '~@assets/less/common.less';
</style>