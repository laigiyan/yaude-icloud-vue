<template>
  <a-modal
    :title="title"
    :width="800"
    :visible="visible"
    :confirmLoading="confirmLoading"
    @ok="handleOk"
    @cancel="handleCancel"
    cancelText="關閉">
    <a-spin :spinning="confirmLoading">
      <a-form-model ref="form"  :label-col="labelCol" :wrapper-col="wrapperCol"  :model="model" :rules="validatorRules">
        <a-form-model-item label="浮動IP" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="floatingIpId" >
          <a-select v-model="model.floatingIpId"  placeholder="請選擇浮動IP">
            <a-select-option v-for="fl in floatingIps":value="fl.value"  >{{fl.text}}</a-select-option>
          </a-select>

        </a-form-model-item>

        <a-form-model-item label="說明"  hasFeedback>
          <span>{{ explain }}</span>
        </a-form-model-item>

      </a-form-model>
    </a-spin>
  </a-modal>
</template>

<script>
  import { getAction,httpAction } from '@/api/manage'

  export default {
    name: "OsFloatingIp",
    data () {
      return {
        title:"綁定浮動IP",
        visible: false,
        model: {},
        layout: {
          labelCol: { span: 3 },
          wrapperCol: { span: 14 },
        },
        labelCol: {
          xs: { span: 24 },
          sm: { span: 3 },
        },
        wrapperCol: {
          xs: { span: 24 },
          sm: { span: 16 },
        },
        confirmLoading: false,
        form: this.$form.createForm(this),
        validatorRules:{
          floatingIpId: [
            { required: true, message: '請選擇浮動IP!' },
          ],
        },
        url: {
          getFloatingIps:"/openstack/osInstance/getFloatingIps",
          addFloatingIp:"/openstack/osInstance/addFloatingIp",
          removeFloatingIP:"/openstack/osInstance/removeFloatingIP",
          getFloatingIpById:"/openstack/osInstance/getFloatingIpById",
        },
        floatingIps:[],
        explain:'請為選中的實例選擇要綁定的IP 位址。'
      }
    },
    created () {

    },
    methods: {
      add () {
        this.edit({});
      },
      edit (record) {
        if(record.floatingIpStatus=='DOWN'){
          this.title = "綁定浮動IP";
          this.explain = '請為選中的實例選擇要綁定的IP 位址';
        }else if(record.floatingIpStatus=='ACTIVE'){
          this.title = "解除浮動IP的綁定";
          this.explain = '選擇從實例上解除關聯的浮動 IP';
        }
        this.getFloatingIps(record);
        this.model = Object.assign({}, record);
        this.visible = true;
      },
      close () {
        this.$refs.form.resetFields();
        this.$emit('close');
        this.visible = false;
      },
      handleOk () {
        const that = this;
        // 觸發表單驗證
        this.$refs.form.validate(valid => {
          if (valid) {
            that.confirmLoading = true;
            let url = this.url.addFloatingIp;
            if(this.model.floatingIpStatus=='DOWN'){
              url = this.url.addFloatingIp;
            }else if(this.model.floatingIpStatus=='ACTIVE'){
              url = this.url.removeFloatingIP;
            }
            getAction(url,this.model).then((res)=>{
              if(res.success){
                that.$message.success(res.message);
                that.$emit('ok');
              }else{
                that.$message.warning(res.message);
              }
            }).finally(() => {
              that.confirmLoading = false;
              that.close();
            })
          }
        })
      },
      handleCancel () {
        this.close()
      },
      //獲取浮動IP
      getFloatingIps(record){
        this.floatingIps = [];

        if(record.floatingIpStatus =='DOWN'){
          getAction(this.url.getFloatingIps,record).then((res)=>{
            if(res.success){
              res.result.forEach((r)=>{
                this.floatingIps.push({
                  value:r.floating_ip_address,
                  text: r.floating_ip_address,
                })
              })
            }
          })
        }else if(record.floatingIpStatus == 'ACTIVE'){
          getAction(this.url.getFloatingIpById,record).then((res)=>{
            if(res.success){
              this.floatingIps.push({
                value:res.result.floatingIp,
                text: res.result.floatingIp
              })
            }
          })
        }

      },
    }
  }
</script>

<style scoped>

</style>