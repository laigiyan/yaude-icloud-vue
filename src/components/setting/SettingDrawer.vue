<template>
  <div class="setting-drawer">
    <a-drawer
      width="300"
      placement="right"
      :closable="false"
      @close="onClose"
      :visible="visible"
      style="height: 100%;overflow: auto;"
    >
      <div class="setting-drawer-index-content">


        <!-- 增加下拉選擇框 -->
        <a-list-item>
          <a-list-item-meta>
            <div slot="title">語言設置</div>
          </a-list-item-meta>
          <a-select size="small" style="width: 80px;" :defaultValue="$t('langValue')" @change="handleLanguageChange">
            <a-select-option value="zh-CN">中文</a-select-option>
            <a-select-option value="zh-TW">繁體中文</a-select-option>
            <a-select-option value="en-US">English</a-select-option>
          </a-select>
        </a-list-item>


        <div :style="{ marginBottom: '24px' }">
          <h3 class="setting-drawer-index-title">整體風格設置{{ $t('langValue') }}</h3>

          <div class="setting-drawer-index-blockChecbox">
            <a-tooltip>
              <template slot="title">
                暗色菜單風格
              </template>
              <div class="setting-drawer-index-item" @click="handleMenuTheme('dark')">
                <img src="https://gw.alipayobjects.com/zos/rmsportal/LCkqqYNmvBEbokSDscrm.svg" alt="dark">
                <div class="setting-drawer-index-selectIcon" v-if="navTheme === 'dark'">
                  <a-icon type="check"/>
                </div>
              </div>
            </a-tooltip>

            <a-tooltip>
              <template slot="title">
                亮色菜單風格
              </template>
              <div class="setting-drawer-index-item" @click="handleMenuTheme('light')">
                <img src="https://gw.alipayobjects.com/zos/rmsportal/jpRkZQMyYRryryPNtyIC.svg" alt="light">
                <div class="setting-drawer-index-selectIcon" v-if="navTheme !== 'dark'">
                  <a-icon type="check"/>
                </div>
              </div>
            </a-tooltip>
          </div>
        </div>

        <div :style="{ marginBottom: '24px' }">
          <h3 class="setting-drawer-index-title">主題色</h3>

          <div style="height: 20px">
            <a-tooltip class="setting-drawer-theme-color-colorBlock" v-for="(item, index) in colorList" :key="index">
              <template slot="title">
                {{ item.key }}
              </template>
              <a-tag :color="item.color" @click="changeColor(item.color)">
                <a-icon type="check" v-if="item.color === primaryColor"></a-icon>
              </a-tag>
            </a-tooltip>

          </div>
        </div>
        <a-divider />

        <div :style="{ marginBottom: '24px' }">
          <h3 class="setting-drawer-index-title">導航模式</h3>

          <div class="setting-drawer-index-blockChecbox">
            <a-tooltip>
              <template slot="title">
                側邊欄導航
              </template>
              <div class="setting-drawer-index-item" @click="handleLayout('sidemenu')">
                <img src="https://gw.alipayobjects.com/zos/rmsportal/JopDzEhOqwOjeNTXkoje.svg" alt="sidemenu">
                <div class="setting-drawer-index-selectIcon" v-if="layoutMode === 'sidemenu'">
                  <a-icon type="check"/>
                </div>
              </div>
            </a-tooltip>

            <a-tooltip>
              <template slot="title">
                頂部欄導航
              </template>
              <div class="setting-drawer-index-item" @click="handleLayout('topmenu')">
                <img src="https://gw.alipayobjects.com/zos/rmsportal/KDNDBbriJhLwuqMoxcAr.svg" alt="topmenu">
                <div class="setting-drawer-index-selectIcon" v-if="layoutMode !== 'sidemenu'">
                  <a-icon type="check"/>
                </div>
              </div>
            </a-tooltip>
          </div>
          <div :style="{ marginTop: '24px' }">
            <a-list :split="false">
              <a-list-item>
                <a-tooltip slot="actions">
                  <template slot="title">
                    該設定僅 [頂部欄導航] 時有效
                  </template>
                  <a-select size="small" style="width: 80px;" :defaultValue="contentWidth" @change="handleContentWidthChange">
                    <a-select-option value="Fixed">固定</a-select-option>
                    <a-select-option value="Fluid" v-if="layoutMode !== 'sidemenu'">流式</a-select-option>
                  </a-select>
                </a-tooltip>
                <a-list-item-meta>
                  <div slot="title">內容區域寬度</div>
                </a-list-item-meta>
              </a-list-item>
              <a-list-item>
                <a-switch slot="actions" size="small" :defaultChecked="fixedHeader" @change="handleFixedHeader" />
                <a-list-item-meta>
                  <div slot="title">固定 Header</div>
                </a-list-item-meta>
              </a-list-item>
              <a-list-item>
                <a-switch slot="actions" size="small" :disabled="!fixedHeader" :defaultChecked="autoHideHeader" @change="handleFixedHeaderHidden" />
                <a-list-item-meta>
                  <div slot="title" :style="{ textDecoration: !fixedHeader ? 'line-through' : 'unset' }">下滑時隱藏 Header</div>
                </a-list-item-meta>
              </a-list-item>
              <a-list-item>
                <a-switch slot="actions" size="small" :disabled="(layoutMode === 'topmenu')" :checked="dataFixSiderbar" @change="handleFixSiderbar" />
                <a-list-item-meta>
                  <div slot="title" :style="{ textDecoration: layoutMode === 'topmenu' ? 'line-through' : 'unset' }">固定側邊菜單</div>
                </a-list-item-meta>
              </a-list-item>
            </a-list>
          </div>
        </div>
        <a-divider />

        <div :style="{ marginBottom: '24px' }">
          <h3 class="setting-drawer-index-title">其他設置</h3>
          <div>
            <a-list :split="false">
              <a-list-item>
                <a-switch slot="actions" size="small" :defaultChecked="colorWeak" @change="onColorWeak" />
                <a-list-item-meta>
                  <div slot="title">色弱模式</div>
                </a-list-item-meta>
              </a-list-item>
              <a-list-item>
                <a-switch slot="actions" size="small" :defaultChecked="multipage" @change="onMultipageWeak" />
                <a-list-item-meta>
                  <div slot="title">多頁簽模式</div>
                </a-list-item-meta>
              </a-list-item>
            </a-list>
          </div>
        </div>
        <a-divider />
        <div :style="{ marginBottom: '24px' }">
          <a-alert type="warning">
            <span slot="message">
              配置欄只在開發環境用於預覽，生產環境不會展現，請手動修改配置文件
              <a href="https://github.com/sendya/ant-design-pro-vue/blob/master/src/defaultSettings.js" target="_blank">src/defaultSettings.js</a>
            </span>
          </a-alert>
        </div>
      </div>
      <div class="setting-drawer-index-handle" @click="toggle" v-if="visible">
<!--        <a-icon type="setting" v-if="!visible"/>-->
<!--        <a-icon type="close" v-else/>-->
        <a-icon type="close" />
      </div>
    </a-drawer>
  </div>
</template>

<script>
  import DetailList from '@/components/tools/DetailList'
  import SettingItem from '@/components/setting/SettingItem'
  import config from '@/defaultSettings'
  import { updateTheme, updateColorWeak, colorList } from '@/components/tools/setting'
  import { mixin, mixinDevice } from '@/utils/mixin.js'
  import { triggerWindowResizeEvent } from '@/utils/util'

  export default {
    components: {
      DetailList,
      SettingItem
    },
    mixins: [mixin, mixinDevice],
    data() {
      return {
        visible: false,
        colorList,
        dataFixSiderbar: false
    }
    },
    mounted () {
      // 當主題色不是默認色時，才進行主題編譯
      if (this.primaryColor !== config.primaryColor) {
        updateTheme(this.primaryColor)
      }
      if (this.colorWeak !== config.colorWeak) {
        updateColorWeak(this.colorWeak)
      }
      if (this.multipage !== config.multipage) {
        this.$store.dispatch('ToggleMultipage', this.multipage)
      }
    },
    methods: {
      showDrawer() {
        this.visible = true
      },
      onClose() {
        this.visible = false
      },
      toggle() {
        this.visible = !this.visible
      },
      onColorWeak (checked) {
        this.$store.dispatch('ToggleWeak', checked)
        updateColorWeak(checked)
      },
      onMultipageWeak (checked) {
        this.$store.dispatch('ToggleMultipage', checked)
      },
      handleMenuTheme (theme) {
        this.$store.dispatch('ToggleTheme', theme)
      },
      handleLayout (mode) {
        this.$store.dispatch('ToggleLayoutMode', mode)
        // 因為頂部菜單不能固定左側菜單欄，所以強制關閉
        this.handleFixSiderbar(false)
        // 觸發窗口resize事件
        triggerWindowResizeEvent()
      },
      handleContentWidthChange (type) {
        this.$store.dispatch('ToggleContentWidth', type)
      },
      changeColor (color) {
        if (this.primaryColor !== color) {
          this.$store.dispatch('ToggleColor', color)
          updateTheme(color)
        }
      },
      handleFixedHeader (fixed) {
        this.$store.dispatch('ToggleFixedHeader', fixed)
      },
      handleFixedHeaderHidden (autoHidden) {
        this.$store.dispatch('ToggleFixedHeaderHidden', autoHidden)
      },
      handleFixSiderbar (fixed) {
        if (this.layoutMode === 'topmenu') {
          fixed = false
        }
        this.dataFixSiderbar = fixed
        this.$store.dispatch('ToggleFixSiderbar', fixed)
      },
      //script  中的  methods 裡增加 handleLanguageChange 切換語言的事件
      handleLanguageChange(lang){
        //this.$i18n.locale = lang; //改變當前語言  動態無感 切換，，但是有部分內容 不生效，估計我
        this.$ls.set("language", lang);//將lang 語言存在localStorage裡，
        //本來想做成，頁面不刷新語言自動切換，但是效果不理想，有的內容切換不成功，於是就 做刷新頁面操作，這裡只緩存語言
        window.location.reload()
      }
    },
  }
</script>

<style lang="less" scoped>

  .setting-drawer-index-content {

    .setting-drawer-index-blockChecbox {
      display: flex;

      .setting-drawer-index-item {
        margin-right: 16px;
        position: relative;
        border-radius: 4px;
        cursor: pointer;

        img {
          width: 48px;
        }

        .setting-drawer-index-selectIcon {
          position: absolute;
          top: 0;
          right: 0;
          width: 100%;
          padding-top: 15px;
          padding-left: 24px;
          height: 100%;
          color: #1890ff;
          font-size: 14px;
          font-weight: 700;
        }
      }
    }
    .setting-drawer-theme-color-colorBlock {
      width: 20px;
      height: 20px;
      border-radius: 2px;
      float: left;
      cursor: pointer;
      margin-right: 8px;
      padding-left: 0px;
      padding-right: 0px;
      text-align: center;
      color: #fff;
      font-weight: 700;

      i {
        font-size: 14px;
      }
    }
  }

  .setting-drawer-index-handle {
    position: absolute;
    top: 240px;
    background: #1890ff;
    width: 48px;
    height: 48px;
    right: 300px;
    display: flex;
    justify-content: center;
    align-items: center;
    cursor: pointer;
    pointer-events: auto;
    z-index: 1001;
    text-align: center;
    font-size: 16px;
    border-radius: 4px 0 0 4px;

    i {
      color: rgb(255, 255, 255);
      font-size: 20px;
    }
  }
</style>
