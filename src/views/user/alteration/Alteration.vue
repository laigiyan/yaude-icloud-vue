<template>
  <a-card :bordered="false" style="width: 130%;text-align: center;margin-left:-10%">
    <a-steps class="steps" :current="currentTab">
      <a-step title="手機驗證"/>
      <a-step title="更改密碼"/>
      <a-step title="完成"/>
    </a-steps>
    <div class="content">
      <step2 v-if="currentTab === 0" @nextStep="nextStep"/>
      <step3 v-if="currentTab === 1" @nextStep="nextStep" @prevStep="prevStep" :userList="userList"/>
      <step4 v-if="currentTab === 2" @prevStep="prevStep" @finish="finish" :userList="userList"/>
    </div>
  </a-card>
</template>

<script>
  import Step2 from './Step2'
  import Step3 from './Step3'
  import Step4 from './Step4'

  export default {
    name: "Alteration",
    components: {
      Step2,
      Step3,
      Step4
    },
    data() {
      return {
        description: '將一個冗長或用戶不熟悉的表單任務分成多個步驟，指導用戶完成。',
        currentTab: 0,
        userList: {},
        // form
        form: null,
      }
    },
    methods: {

      // handler
      nextStep(data) {
        this.userList = data;
        if (this.currentTab < 4) {
          this.currentTab += 1
        }
      },
      prevStep(data) {
        this.userList = data;
        if (this.currentTab > 0) {
          this.currentTab -= 1
        }
      },
      finish() {
        this.currentTab = 0
      }
    }
  }
</script>

<style lang="less" scoped>
  .steps {
    max-width: 750px;
    margin: 16px auto;
  }
  /deep/ .password-retrieval-form{
    max-width: 500px;
    margin: 40px auto 0;
    .ant-form-explain{
      text-align: left;
    }
  }
</style>

