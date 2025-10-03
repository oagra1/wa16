<template>
  <div>
    <div id="guidePage" v-if="isGuide">
      <div v-if="guideStep === 1">
        <img alt="guideStep1" src="pic/guide_1.png" style="width: 100%; height: 100%" />
        <button class="guideButton" @click="guideBtnClick(1)">NEXT STEP</button>
      </div>
      <div v-if="guideStep === 2">
        <img alt="guideStep2" src="pic/guide_2.png" style="width: 100%; height: 100%" />
        <button class="guideStep2Button" @click="guideBtnClick(2)">NEXT STEP</button>
      </div>
      <div v-if="guideStep === 3">
        <img alt="guideStep3" src="pic/guide_3.png" style="width: 100%; height: 100%" />
        <button class="guideSendButton" @click="guideBtnClick(3)" v-if="guideShowSend">Send</button>
        <button class="guideSendButton" v-if="!guideShowSend" style="cursor: not-allowed">
          Sending...
        </button>
      </div>
    </div>
    <div v-if="showBtnMsgImg">
      <img
        alt="guideStep4"
        src="pic/btn_msg.jpg"
        style="width: 100%; height: 100%"
        @click="exchangeBtnMsgImg"
      />
    </div>
    <div id="app" v-if="!isGuide" style="background-color: #f9f9f9">
      <div class="hello">
        <TopInfo
          @openActiveCodePopup="openActiveCodePopup"
          :permissionText="permissionText"
          @openPricing="openPricing"
          @openUserProfile="openUserProfile"
        ></TopInfo>
        <el-tabs v-model="activeName" @tab-click="handleClick" :class="headTab">
          <el-tab-pane name="strengthen">
            <el-tooltip
              v-if="$i18n.locale === 'pt'"
              slot="label"
              class="item"
              effect="dark"
              :content="strengthenText"
              placement="top-start"
            >
              <div>
                <span
                  slot="label"
                  style="
                    color: white;
                    padding-left: 16px;
                    display: block;
                    overflow: hidden;
                    text-overflow: ellipsis;
                  "
                  v-text="strengthenText"
                ></span>
              </div>
            </el-tooltip>
            <span
              v-else
              slot="label"
              style="
                color: white;
                padding-left: 16px;
                display: block;
                overflow: hidden;
                text-overflow: ellipsis;
              "
              v-text="strengthenText"
            ></span>
          </el-tab-pane>
          <el-tab-pane v-if="!waplus_remove_flag" name="send-messages">
            <el-tooltip
              v-if="$i18n.locale === 'pt'"
              slot="label"
              class="item"
              effect="dark"
              :content="sendMessagesText"
              placement="top-start"
            >
              <div>
                <span
                  slot="label"
                  style="
                    color: white;
                    padding-left: 16px;
                    display: block;
                    overflow: hidden;
                    text-overflow: ellipsis;
                  "
                  v-text="sendMessagesText"
                ></span>
              </div>
            </el-tooltip>
            <span
              v-else
              slot="label"
              style="color: white; padding-left: 16px"
              v-text="sendMessagesText"
            ></span>
          </el-tab-pane>
          <el-tab-pane name="send-messages-pro">
            <el-tooltip
              v-if="$i18n.locale === 'pt'"
              slot="label"
              class="item"
              effect="dark"
              :content="sendMessagesProText"
              placement="top-start"
            >
              <div>
                <span
                  slot="label"
                  style="
                    color: white;
                    padding-left: 16px;
                    display: block;
                    overflow: hidden;
                    text-overflow: ellipsis;
                  "
                  v-text="sendMessagesProText"
                >
                </span>
              </div>
            </el-tooltip>
            <span
              v-else
              slot="label"
              style="color: white; padding-left: 16px"
              v-text="sendMessagesProText"
            >
            </span>
          </el-tab-pane>

          <el-tab-pane name="group-tools">
            <el-tooltip
              v-if="$i18n.locale === 'pt'"
              slot="label"
              class="item"
              effect="dark"
              :content="exportGroupText"
              placement="top-start"
            >
              <div>
                <span
                  slot="label"
                  style="color: white; display: block; overflow: hidden; text-overflow: ellipsis"
                  v-text="exportGroupText"
                ></span>
              </div>
            </el-tooltip>
            <span v-else slot="label" style="color: white" v-text="exportGroupText"></span>
          </el-tab-pane>
          <el-tab-pane name="more-tools">
            <el-tooltip
              v-if="$i18n.locale === 'pt'"
              slot="label"
              class="item"
              effect="dark"
              :content="moreToolsText"
              placement="top-start"
            >
              <div>
                <span
                  slot="label"
                  style="
                    color: white;
                    padding-right: 16px;
                    display: block;
                    overflow: hidden;
                    text-overflow: ellipsis;
                  "
                  v-text="moreToolsText"
                ></span>
              </div>
            </el-tooltip>
            <span
              v-else
              slot="label"
              style="
                color: white;
                padding-right: 16px;
                display: block;
                overflow: hidden;
                text-overflow: ellipsis;
              "
              v-text="moreToolsText"
            ></span>
          </el-tab-pane>
        </el-tabs>
        <Advertisement
          @openPricing="openPricing"
          @openActiveCodePopup="openActiveCodePopup"
        ></Advertisement>
        <div
          class="tabContainer"
          :class="activeName === 'send-messages' ? 'grey-background-color' : ''"
        >
          <Strengthen v-if="activeName === 'strengthen'" />

          <SendMessages
            ref="messageSimple"
            :permissionText="permissionText"
            @openPricing="openPricing"
            v-if="activeName === 'send-messages' && !showStatistics"
            :setShowStatistics="setShowStatistics"
          ></SendMessages>

          <SendMessagesPro
            :groupsMap="groupsMap"
            :groupOptions="groupOptions"
            :labelOptions="labelOptions"
            :isBusiness="isBusiness"
            ref="messagePro"
            v-if="activeName === 'send-messages-pro' && !showStatistics"
            @openPayPermissionPopup="openPayPermissionPopup"
            :permissionText="permissionText"
            @openPricing="openPricing"
            :setShowStatistics="setShowStatistics"
          ></SendMessagesPro>

          <Statistics
            ref="Statistics"
            :isShowContinueBtn="isShowContinueBtn"
            :disabledSendingFlag="disabledSendingFlag"
            :countAll="countAll"
            :countTotal="countTotal"
            :deduplicated="deduplicated"
            :countFail="countFail"
            :countSuccess="countSuccess"
            :waitSeconds="waitSeconds"
            v-if="
              (activeName === 'send-messages' || activeName === 'send-messages-pro') &&
              showStatistics
            "
            @openPayPermissionPopup="openPayPermissionPopup"
            :permissionText="permissionText"
            @openPricing="openPricing"
            :setShowStatistics="setShowStatistics"
          ></Statistics>

          <GroupTools
            :chooseWarning="chooseWarning"
            :beRemovedWarning="beRemovedWarning"
            :groupsMap="groupsMap"
            :groupOptions="groupOptions"
            :labelOptions="labelOptions"
            :isBusiness="isBusiness"
            v-if="activeName === 'group-tools'"
            @openPayPermissionPopup="openPayPermissionPopup"
            :permissionText="permissionText"
            @openPricing="openPricing"
          ></GroupTools>

          <el-card
            shadow="always"
            :body-style="{ padding: '0px 0px 20px' }"
            style="margin: 11px 0px 10px; padding: 0 10px"
            v-if="activeName === 'more-tools'"
          >
            <MoreToolsVue></MoreToolsVue>
          </el-card>

          <!-- 确认发送弹窗 -->
          <el-dialog
            :visible.sync="dialogVisible"
            width="459px"
            top="125px"
            :modal-append-to-body="false"
            :show-close="true"
            :close-on-click-modal="false"
            @open="confirmSendDialogOpen"
            @close="confirmSendDialogClose"
            custom-class="sendConfirmPop"
            center
          >
            <div style="margin-bottom: 23px; font-weight: bold; font-style: normal; color: #0f0101">
              To avoid sending failed , we suggest:
            </div>
            <ul class="sendConfirmUl" style="margin: 23px 0 35px 15px">
              <li>
                <i class="boldFont">Do not switch the chat window.</i>
                <br />
                <span>Stay here while messages are sending automatically.</span>
              </li>
            </ul>
            <div>
              <a href="#" @click="openWaHelpCenter">About linked devices.</a>
            </div>
            <div class="safetyFooter" slot="footer">
              <el-button type="success" @click="confirmSendDialog">Agree & Continue</el-button>
              <br />
              <el-checkbox
                style="margin-top: 11px"
                v-model="notShowAgainModel"
                @change="notShowAgain"
                >Don't show again
              </el-checkbox>
            </div>
          </el-dialog>

          <!-- 为空弹窗 -->
          <el-dialog
            :visible.sync="concatsIsNull"
            width="459px"
            top="220px"
            class="concatsPop"
            :modal-append-to-body="false"
            center
          >
            <div style="margin-bottom: 55px; height: 24px; line-height: 24px">
              <i
                class="el-icon-warning"
                style="color: red; margin: 0 16px; font-size: 24px; vertical-align: middle"
              ></i>
              <span style="color: red">Please enter at least one number.（ ≥ 1 number）</span>
            </div>
          </el-dialog>

          <!-- 安全警示页弹窗 -->
          <el-dialog
            :visible.sync="isShowSafetyWarningFlagValue"
            width="459px"
            top="120px"
            custom-class="safetyPop"
            :modal-append-to-body="false"
            :show-close="true"
            :close-on-click-modal="false"
            @open="openSafetyDialog"
            @close="closeSafetyDialog"
            center
          >
            <div
              slot="title"
              style="font-size: 18px; font-weight: bold; font-style: normal; color: #0f0101"
            >
              Safety Suggestions
            </div>
            <div
              style="
                margin: -10px 0 0 30px;
                height: 25px;
                line-height: 25px;
                font-weight: bold;
                font-style: normal;
                color: #0f0101;
              "
            >
              To avoid your account being banned, we suggest:
            </div>
            <ul class="safetyFirstUl">
              <li>
                <span>Account has been registered <i class="boldFont">≥ 15</i> days</span>
              </li>
              <li>
                <span
                  >Mobile phone and website <i class="boldFont">IP are in the same area</i></span
                >
              </li>
              <li>
                <span><i class="boldFont">Do not</i> switch IP frequently</span>
              </li>
            </ul>
            <div>
              <div style="margin: 19px 0 0 30px; height: 25px; line-height: 25px">
                <i class="boldFont">Messages you could send:</i>
              </div>
              <ul class="safetySecondUl">
                <li>
                  <span>50 max - the first day <i class="boldFont">(≤24h)</i></span>
                </li>
                <li>
                  <span>300 max - from second day <i class="boldFont">(>24h)</i></span>
                </li>
              </ul>
            </div>
            <div class="safetyFooter" slot="footer">
              <el-button type="success" @click="updateShowSafetyWarningFlag"
                >Agree & Continue</el-button
              >
              <br />
              <el-checkbox
                style="margin-top: 11px"
                v-model="isDontShowSafetyWarning"
                @change="againCheckBoxChange"
                >Don't show again
              </el-checkbox>
            </div>
          </el-dialog>
          <ActiveCodeDialog
            :isShowActiveCode="isShowActiveCode"
            @closeActiveCodePopup="closeActiveCodePopup"
            @changePermissionCode="changePermissionCode"
            @openPricing="openPricing"
          ></ActiveCodeDialog>
          <PermissionDialog
            :isShowPayPermissionPopup="isShowPayPermissionPopup"
            @closePayPermissionPopup="closePayPermissionPopup"
            @openPricing="openPricing"
          ></PermissionDialog>
          <UserProfile
            :isShowUserProfile="isShowUserProfile"
            @openPricing="openPricing"
            @closeUserProfilePopup="closeUserProfilePopup"
          ></UserProfile>

          <Suggestion
            :isShowSuggestion="isShowSuggestion"
            :maxNum="maxSleep"
            :countFail="countFail"
            @closeSuggestion="closeSuggestion"
            @openPricing="openPricing"
          ></Suggestion>
          <!-- 没有使用激活码弹窗 -->
          <NoActionDialog
            v-if="isShowNoActive"
            :isShowNoActive="isShowNoActive"
            @changePermissionCode="changePermissionCode"
            @closeNoActivePopup="closeNoActivePopup"
          ></NoActionDialog>

          <!-- 没有续订弹窗 -->
          <NoSubscription
            v-if="isShowNoSubscription"
            :isShowNoSubscription="isShowNoSubscription"
            @openPricing="openPricing"
            @openOrder="openOrder"
            @closeNoSubscription="closeNoSubscription"
          ></NoSubscription>

          <!-- 1美元没有续订弹窗 -->
          <RenewalRecomDialog
            v-if="isOneNoSubscription"
            :isShowRenewalRecomDialog="isOneNoSubscription"
            @openPricing="openPricing"
            @supportUs="supportUs"
            @closeOneNoSubscription="closeOneNoSubscription"
          >
          </RenewalRecomDialog>
          <!-- Button Message Dialog -->
          <ButtonMessageDialog
            :buttonMsgDialogVisible.sync="buttonMsgDialogVisible"
            :showBtnMsgImg.sync="showBtnMsgImg"
            ref="buttonMessageDialog"
          ></ButtonMessageDialog>
        </div>
        <CommonBottomHref
          v-bind:sendType="activeName"
          :group-options="groupOptions"
        ></CommonBottomHref>
        <GeneralComments :googleUrl="googleUrl" logId="900013"></GeneralComments>

        <!-- 临时引流文件 -->
        <drainage :isShowDrainageDialog="isShowDrainageDialog"></drainage>
      </div>
    </div>
  </div>
</template>

<script>
import XLSX from 'xlsx'
import CommonBottomHref from '@/components/common/BottomHref'
import { getBrowser } from '@/utils/browser-util'

import TopInfo from '@/components/topInfo/TopInfo'
import SendMessages from '@/pages/sendMessages/SendMessages'
import SendMessagesPro from '@/pages/sendMessagesPro/SendMessagesPro'
import GroupTools from '@/pages/groupTools/GroupTools'
import Statistics from '@/pages/statistics/Statistics'
import Strengthen from '@/pages/strengthen/Strengthen'
import { WAPLUS_REMOVE_FLAG } from '@/utils/extension-util'
import ActiveCodeDialog from '@/components/common/ActiveCodeDialog'
// import { PRICING_PRO_PLAN_URL } from '@/config/pay-config'
import { createNewWindowPage } from '@/utils/chrome-command-util'
import PermissionDialog from '@/components/common/PermissionDialog'
import {
  PAY_HTML_FILE_PATH_URL,
  MIGRATION_SIMPLE_FLOW,
  PLAN_PAGE_URL
} from '@/service/constants'
import UserProfile from '@/pages/user/UserProfile'
import Suggestion from '@/common/freeDialog/Suggestion'
import { loggerFeatureId } from '@/utils/log-util'
import NoActionDialog from '@/common/proDialog/NoActionDialog'
import ButtonMessageDialog from '@/components/common/ButtonMessageDialog.vue'
import NoSubscription from '@/common/proDialog/NoSubscription'
import RenewalRecomDialog from '@/common/proDialog/RenewalRecomDialog'
import { getPayUrl } from '@/api/permission'
import MoreToolsVue from '@/pages/moreTools/MoreTools.vue'
import { sendMessage } from 'webext-bridge/dist/popup'
import {
  POP_TO_CONTENT_TRANSACTION_ID,
  CONTENT_TO_POP_IS_SHOW_NO_ACTIVE,
  CONTENT_TO_POP_IS_SHOW_NO_SUBSCRIPTION,
  CONTENT_TO_POP_IS_ONE_NO_SUBSCRIPTION,
  CONTENT_TO_POP_SHOW_SAVE_TIME_DIALOG,
  CONTENT_TO_POP_GET_PRO_PERMISSION
} from '@/service/constants.js'
import Advertisement from '../components/common/Advertisement.vue'
import GeneralComments from '@/components/generalComments/GeneralComments.vue'
import { GOOGLE_URL } from '@/config/dict.js'
import Drainage from './drainage/index.vue'
export default {
  name: 'App',
  components: {
    CommonBottomHref,
    TopInfo,
    SendMessages,
    SendMessagesPro,
    GroupTools,
    Statistics,
    Strengthen,
    ActiveCodeDialog,
    PermissionDialog,
    UserProfile,
    Suggestion,
    NoActionDialog,
    NoSubscription,
    RenewalRecomDialog,
    MoreToolsVue,
    ButtonMessageDialog,
    Advertisement,
    GeneralComments,
    Drainage
  },
  data() {
    return {
      showStatistics: false, // 是否显示数据看板
      isBusiness: false, // 是否是商业版
      headTab: this.$i18n.locale === 'pt' ? 'headerTab pt' : 'headerTab',
      locale: this.$i18n.locale,
      guideShowSend: true,
      isGuide: false,
      guideStep: 1,
      // 控制手机号是否是空的弹窗的弹出
      concatsIsNull: false,
      // 控制是否展示安全警示页
      isShowSafetyWarningFlagValue: false,
      // 安全警示页下面的勾选下次是否还展示
      isDontShowSafetyWarning: false,
      // sendMessage模块上面的Tab内容
      // sendMessagesText: this.$t('send_messages_t'),
      // sendMessagePro模块上面的Tab内容
      // sendMessagesProText: this.$t('send_messages_pro_t'),
      // 控制当前展示的tab
      activeName: 'send-messages',
      sendMessageType: 'simple',
      // 数据统计页面里面会依据这个判断一些内容
      sendMessageStatisticsType: 'simple',
      groupOptions: [],
      groupsMap: {},
      labelOptions: [],
      chooseWarning: false,
      beRemovedWarning: false,
      // strengthenText: this.$t('strengthen_t'),
      // exportGroupText: this.$t('group_tools_t'),
      sleepText: 'Help',
      countSuccess: 0,
      maxSleep: 0, // 最大睡眠时间
      countFail: 0,
      countAll: 0,
      // 发送消息的消息总数
      countTotal: 0,
      // 去重之后发送消息的总量
      deduplicated: 0,
      waitSeconds: 0,
      firstLoadValue: false,
      selected: '',
      dropdownMap: {
        'User Guide': 'https://waplus.io/sender-help',
        'Contact Us':
          'https://api.whatsapp.com/send/?phone=%2B8617891209984&text=Hi+WAPlus+Sender%21+I+have+a+question.'
      },
      // 控制确认发送的展示与否
      dialogVisible: false,
      // 每日发送的数量
      dailySendNums: 0,
      phoneNumList: [],
      isShowContinueBtn: false,
      loadingPage: null,
      disabledSendingFlag: true,
      // 首次发送消息的时间
      firstSendTime: '',
      browserType: 'Chrome',
      // 控制确认发送的弹窗下次是否还展示
      notShowAgainModel: false,
      waplus_remove_flag: WAPLUS_REMOVE_FLAG,
      isShowActiveCode: false,
      isShowPayPermissionPopup: false,
      permissionCode: '',
      paidMark: false,
      myappActivation: false,
      permissionInfo: null,
      isShowUserProfile: false,

      // 是否展示用户建议
      isShowSuggestion: false,
      // 是否展示没有使用激活码弹窗
      isShowNoActive: false,
      // 是否展示没有续订弹窗
      isShowNoSubscription: false,
      // 是否展示一美元没有续订弹窗
      isOneNoSubscription: false,
      // moreToolsText: this.$t('more_tools_t'),
      showBtnMsgImg: false,
      buttonMsgDialogVisible: false,
      googleUrl: GOOGLE_URL,
      // 临时引流
      isShowDrainageDialog: false
    }
  },
  computed: {
    isPro() {
      const legacy = !!this.permissionCode
      const paid = this.paidMark === true
      const activated = this.myappActivation === true
      const statusActive = this.permissionInfo && this.permissionInfo.status === 'active'
      return legacy || paid || activated || statusActive
    },
    permissionText() {
      console.log('🔄 [COMPUTED] Executando...')
      console.log('🔄 [COMPUTED] permissionCode atual:', this.permissionCode)
      const result = this.isPro ? 'Pro' : 'Free'
      console.log('🔄 [COMPUTED] Retornando:', result)
      return result
    },
    pauseAndSendBtn() {
      return {
        countAll: this.countAll,
        countSuccess: this.countSuccess,
        countFail: this.countFail,
        waitSendNum: this.countAll - this.countSuccess
      }
    },
    disabledSendingFlagData() {
      return {
        countAll: this.countAll,
        waitSeconds: this.waitSeconds
      }
    },
    sendMessagesText() {
      return this.$t('send_messages_t')
    },
    sendMessagesProText() {
      return this.$t('send_messages_pro_t')
    },
    strengthenText() {
      return this.$t('strengthen_t')
    },
    exportGroupText() {
      return this.$t('group_tools_t')
    },
    moreToolsText() {
      return this.$t('more_tools_t')
    }
  },
  methods: {
    /**
     * @description: 赋予免费用户试用权限
     * @return {*}
     */
    async grantTrialPermission() {
      if (this.permissionText === 'Pro') return
      const { trialExpirationTime } = await chrome.storage.local.get(['trialExpirationTime'])
      if (trialExpirationTime && trialExpirationTime > new Date().getTime()) {
        this.permissionCode = 'Pro'
      }
    },
    async getLabelOptionsAndGroupOptions() {
      if (this.labelOptions.length === 0 || this.groupOptions.length === 0) {
        // this.$emit('getLabelOptions')
        console.log('发送消息')
        const tab = await chrome.tabs.query({ active: true, currentWindow: true })
        // 校验tab
        this.$bridge.sendMessage('POP_TO_CONTENT_GET_LABELS', {}, `content-script@${tab[0].id}`)
      }
    },
    setShowStatistics(data) {
      this.showStatistics = data
    },
    exchangeBtnMsgImg() {
      this.showBtnMsgImg = false
    },
    openUserProfile() {
      this.isShowUserProfile = true
    },
    closeUserProfilePopup() {
      this.isShowUserProfile = false
    },
    closeNoSubscription() {
      this.isShowNoSubscription = false
    },
    closeOneNoSubscription() {
      this.isOneNoSubscription = false
    },
    closeNoActivePopup() {
      this.isShowNoActive = false
    },
    closeSuggestion() {
      this.isShowSuggestion = false
    },
    async restoreProStatus() {
      console.log('📦 [RESTORE] Iniciando restore...')
      return new Promise((resolve) => {
        chrome.storage.local.get(
          ['paid_mark', 'myapp_activation', 'permissionInfo', 'permissionCode', 'myapp_license'],
          (result) => {
            console.log('📦 [RESTORE] Storage completo:', result)
            console.log('📦 [RESTORE] paid_mark:', result.paid_mark)
            console.log('📦 [RESTORE] myapp_activation:', result.myapp_activation)
            console.log('📦 [RESTORE] myapp_license:', result.myapp_license)
            console.log('📦 [RESTORE] permissionInfo:', result.permissionInfo)
            console.log('📦 [RESTORE] permissionCode do storage:', result.permissionCode)

            this.paidMark = !!result.paid_mark
            this.myappActivation = !!result.myapp_activation
            this.permissionInfo = result.permissionInfo || null

            const hasActiveLicense =
              (this.paidMark && this.myappActivation && !!result.myapp_license) ||
              (this.permissionInfo && this.permissionInfo.status === 'active')

            console.log('📦 [RESTORE] hasActiveLicense?', hasActiveLicense)

            if (hasActiveLicense) {
              const restoredCode =
                (this.permissionInfo && this.permissionInfo.plink_id) ||
                result.permissionCode ||
                'supabase_pro'
              console.log('📦 [RESTORE] Código Pro calculado:', restoredCode)
              console.log('📦 [RESTORE] ANTES de setar - this.permissionCode:', this.permissionCode)

              this.permissionCode = restoredCode
              console.log('📦 [RESTORE] DEPOIS de setar - this.permissionCode:', this.permissionCode)
              if (result.permissionCode !== restoredCode) {
                console.log('📦 [RESTORE] Salvando permissionCode no storage...')
                chrome.storage.local.set({ permissionCode: restoredCode })
              }
            } else if (this.permissionCode) {
              console.log('📦 [RESTORE] Sem licença ativa, limpando permissionCode')
              this.permissionCode = ''
              if (result.permissionCode) {
                console.log('📦 [RESTORE] Limpando permissionCode no storage...')
                chrome.storage.local.set({ permissionCode: '' })
              }
            } else {
              console.log('📦 [RESTORE] Sem licença ativa e permissionCode já vazio')
            }

            console.log('📦 [RESTORE] Finalizando...')
            resolve()
          }
        )
      })
    },
    async changePermissionCode(permissionCode, transaction_id, whatsappNumber) {
      if (permissionCode) {
        this.permissionCode = permissionCode
      } else {
        const shouldMarkPro =
          this.paidMark === true ||
          this.myappActivation === true ||
          (this.permissionInfo && this.permissionInfo.status === 'active')
        if (shouldMarkPro && !this.permissionCode) {
          this.permissionCode = 'supabase_pro'
        } else if (!shouldMarkPro && this.permissionCode === 'supabase_pro') {
          this.permissionCode = ''
        }
      }

      chrome.storage.local.set({ permissionCode: this.permissionCode || '' })
      const tab = await chrome.tabs.query({
        active: true,
        currentWindow: true
      })
      const zb = await sendMessage(
        POP_TO_CONTENT_TRANSACTION_ID,
        { whatsappNumber: whatsappNumber, transaction_id: transaction_id },
        `content-script@${tab[0].id}`
      )
      // eslint-disable-next-line no-console
      // console.log(zb)
    },
    openPricing(type) {
      if (MIGRATION_SIMPLE_FLOW) {
        try {
          if (chrome && chrome.tabs && chrome.tabs.create) {
            chrome.tabs.create({ url: PLAN_PAGE_URL })
          } else {
            window.open(PLAN_PAGE_URL, '_blank')
          }
        } catch (error) {
          console.error('[MIG] openPricing', error)
        }
        return
      }
      let _that = this
      chrome.storage.local.get(
        [
          'paid_mark',
          'permissionInfo',
          'browserInfo',
          'platform',
          'installTime',
          'userPhoneNum',
          'firstSendTime',
          'uuid',
          'version',
          'language',
          'zbaseConfig'
        ],
        async (resp) => {
          const planArr = resp.zbaseConfig.data.config.filter((item) => {
            return item.name === 'planConfigs'
          })
          const planList = await _that.getPlanData(planArr[0].params)
          _that.openPlanPage(resp, planList, type)
          if (_that.permissionText === 'Free') {
            _that.openActiveCodePopup()
          }
        }
      )
    },
    /**
     * @description: 打开套餐页面
     * @param {*} resp 本地存储数据
     * @return {*}
     */
    async openPlanPage(resp, planList, type) {
      if (MIGRATION_SIMPLE_FLOW) {
        return
      }
      const PermissionInfoStr = this.getPermissionInfo(resp, type, planList)
      const PRICING_PRO_PLAN_URL = await chrome.runtime.getURL(PAY_HTML_FILE_PATH_URL)
      const url = new URL(PRICING_PRO_PLAN_URL)
      const params = url.searchParams
      let browserInfo = resp.browserInfo ?? {}
      params.set('planList', JSON.stringify(planList))
      params.set('install_time', resp.installTime ?? '')
      params.set('s_first_send_time', resp.firstSendTime ?? '')
      params.set('platform', resp.platform ?? '')
      params.set('user_agent', browserInfo?.userAgent ?? '')
      params.set('browser_type', browserInfo?.browser ?? '')
      params.set('uuid', resp.uuid ?? '')
      params.set('version', resp.version ?? '')
      params.set('version_current', chrome.runtime.getManifest().version ?? '')
      params.set('language', resp.language ?? '')
      params.set('s_user_phone_num', resp.userPhoneNum ?? '')
      params.set(
        'origin_data',
        JSON.stringify({
          whatsapp_number: resp.userPhoneNum ?? '',
          is_renew: 0
        })
      )
      // console.log(PermissionInfoStr)
      createNewWindowPage(url.toString() + '&permissionInfo=' + PermissionInfoStr)
    },
    /**
     * @description:根据用户以存在的套餐，添加日志
     * @return {*}
     */
    getPermissionInfo(storage, type, planList) {
      let str = ''
      if (storage.permissionInfo != undefined) {
        const index = planList.findIndex((item) => {
          return storage.permissionInfo.plink_id === item.code
        })
        if (index !== -1) {
          str = planList[index].planName
        } else if (storage.permissionInfo.plink_id === 'plink_1MNCsxBNqRnfJH4PP4ULmogk') {
          str = '1_7'
        }
      }
      if (type === 'top') {
        this.$sendLog(908307, { s_plan: str })
      }
      if (type === 'user') {
        this.$sendLog(908308, { s_plan: str })
      }
    },
    /**
     * @description: 结合用户所在国家,过滤套餐信息
     * @param {*} planList 原始套餐信息
     * @return {*}
     */
    async getPlanData(planList) {
      const res = await chrome.storage.local.get('userIpInfo')
      let userIpInfo = null
      if (res.userIpInfo && res.userIpInfo.countryCode) {
        userIpInfo = res.userIpInfo
      } else {
        userIpInfo = {
          status: 'success',
          country: 'United States',
          countryCode: 'US',
          region: 'CA',
          regionName: 'California',
          city: 'Los Angeles',
          zip: '90060',
          lat: 34.0544,
          lon: -118.2441,
          timezone: 'America/Los_Angeles',
          isp: 'NetLab Global',
          org: 'Performance Systems International',
          as: 'AS979 NetLab Global',
          query: '154.9.249.239'
        }
      }

      planList = planList.filter((item) => {
        if (item.whitelist) {
          return item.inCountryList.some((tepItem) => {
            return tepItem == userIpInfo.countryCode
          })
        } else {
          return !item.exCountryList.some((tepItem) => {
            return tepItem == userIpInfo.countryCode
          })
        }
      })
      return planList
    },
    // 当前套餐的支付链接
    openOrder() {
      if (MIGRATION_SIMPLE_FLOW) {
        const message =
          this.$t?.('plans.externalRedirect') ||
          'Please complete or renew your plan on the external plans page.'
        if (this.$message && typeof this.$message.info === 'function') {
          this.$message.info(message)
        } else if (this.$message) {
          this.$message({ type: 'info', message })
        } else {
          console.info('[MIG] openOrder info:', message)
        }
        return
      }
      chrome.storage.local.get(['permissionInfo', 'userPhoneNum'], function (res) {
        if (res.permissionInfo && 'plink_id' in res.permissionInfo) {
          getPayUrl(res.permissionInfo['plink_id'], res.userPhoneNum, 1).then((response) => {
            if (response.code === 100000) {
              window.open(response.data.pay_link)
            } else {
              // todo 修复
              this.$message({
                message: response.message,
                type: 'error'
              })
            }
          })
        }
      })
    },
    closePayPermissionPopup() {
      this.isShowPayPermissionPopup = false
    },
    openPayPermissionPopup(e) {
      this.$sendLog(908303, { s_feature_id: e.featureId })
      this.isShowPayPermissionPopup = true
    },
    closeActiveCodePopup() {
      this.isShowActiveCode = false
    },
    openActiveCodePopup() {
      this.isShowActiveCode = true
    },
    guideBtnClick(step) {
      if (step === 1) {
        this.guideStep = 2
        this.$sendLog(306201)
      } else if (step === 2) {
        this.guideStep = 3
        this.$sendLog(306202)
      } else {
        this.guideClose()
        this.$sendLog(306203)
      }
    },
    guideClose() {
      this.guideShowSend = false
      this.sendPrivateDomain()
    },
    sendPrivateDomain() {
      chrome.tabs.query({ active: true, currentWindow: true }, function (tabs) {
        chrome.tabs.sendMessage(tabs[0].id, { sendPrivateDomain: true })
      })
    },
    openWaHelpCenter() {
      window.open('https://faq.whatsapp.com/general/download-and-installation/about-linked-devices')
    },
    // 确认发送弹窗打开的时候触发
    confirmSendDialogOpen() {
      chrome.storage.local.set({ sendMessageFlag: false })
      let _This = this
      // todo 为什么这块会有confirmSendData这个字段
      chrome.storage.local.get('confirmSendData', function (res) {
        let type = res['confirmSendData']['sendMessageType']
        // 运行前提示页的弹出
        if (type === 'simple') {
          _This.$sendLog(900206, { s_type: 1 })
        }
        if (type === 'pro') {
          _This.$sendLog(900206, { s_type: 2 })
        }
      })
    },
    // 确认发送弹窗关闭的时候触发
    confirmSendDialogClose() {
      let _This = this
      chrome.storage.local.get('confirmSendData', function (res) {
        let type = res['confirmSendData']['sendMessageType']
        // 运行前提示页的关闭
        if (type === 'simple') {
          _This.$sendLog(900207, { s_type: 1 })
        }
        if (type === 'pro') {
          _This.$sendLog(900207, { s_type: 2 })
        }
      })
    },
    // 明确要发送之后调用的方法
    confirmSendDialog() {
      // 1、先将明确发送的弹窗关闭
      this.dialogVisible = false
      // 2、如果我们选择了下次不再发送，storage里面存储：dialogVisible让下次判断是否展示
      if (this.notShowAgainModel) {
        chrome.storage.local.set({ dialogVisible: false })
      }
      this.setShowStatistics(true)
      const _this = this
      /*
       * todo 四个storage里面的内容
       *  confirmSendData
       *  allDataImg
       *  allDataVideo
       *  allDataDocument
       *  confirmSetData
       * */
      chrome.storage.local.get(
        [
          'confirmSendData',
          'allDataImg',
          'allDataVideo',
          'allDataDocument',
          'confirmSetData',
          'audio'
        ],
        (res) => {
          // todo 为什么要重新塞入一下～
          if (res.confirmSetData) {
            chrome.storage.local.set(res.confirmSetData)
          }
          // 如果res.confirmSendData内容正常
          if (typeof res.confirmSendData === 'object' && res.confirmSendData !== null) {
            // _this.updateDisabledSendingFlag(false)
            let confirmSendData = res.confirmSendData
            confirmSendData.allDataImg = res.allDataImg ? res.allDataImg : []
            confirmSendData.allDataVideo = res.allDataVideo ? res.allDataVideo : []
            confirmSendData.allDataDocument = res.allDataDocument ? res.allDataDocument : []
            confirmSendData.audio = res.audio || {}
            let type = res['confirmSendData']['sendMessageType']
            // 确认发送之后的日志
            if (type === 'simple') {
              _this.$sendLog(900208, { s_type: 1 })
            }
            if (type === 'pro') {
              _this.$sendLog(900208, { s_type: 2 })
            }
            _this.sendDataPopToContent(confirmSendData)
          }
        }
      )
      // todo sendMessageFlag 确认发送消息之后的去二人
      chrome.storage.local.set({ sendMessageFlag: true })
    },
    // 这个方法在MessageSimple和Pro里面调用的，发消息必定会调用
    handleConfirmClick() {
      this.$nextTick(() => {
        this.sendMessageStatisticsType = this.sendMessageType
        let content = ''
        let phoneNum = ''
        let mediaType = ''
        let minNum = ''
        let maxNum = ''
        let sentSilent = ''
        let uploadExcel = ''
        let stopFlag = ''
        let stopFlagSimple = ''
        let groupConcats = ''
        let labelConcats = ''
        let sendButtonValue = false
        let button_radio = ''
        let buttonInputValue = ''
        let isDeleteMessage = false
        const _this = this
        if (this.sendMessageType === 'pro') {
          content = this.$refs.messagePro.messageText
          phoneNum = this.$refs.messagePro.whatsAppNumbers
          if (this.$refs.messagePro.radio === 'Image') {
            mediaType = 'img'
          }
          if (this.$refs.messagePro.radio === 'Video') {
            mediaType = 'video'
          }
          if (this.$refs.messagePro.radio === 'Document') {
            mediaType = 'doc'
          }
          if (this.$refs.messagePro.radio === 'Contact') {
            mediaType = 'contact'
          }
          sendButtonValue = this.$refs.messagePro.sendButtonValue
          button_radio = this.$refs.messagePro.button_radio
          buttonInputValue = this.$refs.messagePro.buttonInputValue
          minNum = this.$refs.messagePro.minNum
          maxNum = this.$refs.messagePro.maxNum
          uploadExcel = this.$refs.messagePro.uploadExcelValue
          groupConcats = this.$refs.messagePro.groupConcats
          labelConcats = this.$refs.messagePro.labelConcats
          isDeleteMessage = this.$refs.messagePro.isDeleteMessage
          stopFlag = false
          stopFlagSimple = 'disabled'
        }
        if (this.sendMessageType === 'simple') {
          content = this.$refs.messageSimple.messageTextSimple
          phoneNum = this.$refs.messageSimple.whatsAppNumbersSimple
          minNum = this.$refs.messageSimple.minNumSimple
          maxNum = this.$refs.messageSimple.maxNumSimple
          // todo 没有看清楚stopFlag是用来做什么的
          stopFlag = 'disabled'
          stopFlagSimple = false
        }
        this.countAll = 0
        this.countTotal = 0
        this.deduplicated = 0
        this.countSuccess = 0
        this.countFail = 0
        this.waitSeconds = 0
        //保存要发送的数据，因为在statistics获取不到pro或者simple模块数据
        const confirmSetData = {
          stopFlag,
          stopFlagSimple,
          sendMessageType: _this.sendMessageType,
          countAll: 0,
          countTotal: 0,
          deduplicated: 0,
          countSuccess: 0,
          countFail: 0,
          // activeName: 'statistics',
          phoneNumList: [],
          phoneNumSuccessList: [],
          phoneNumFailList: [],
          sendMessageStatisticsType: _this.sendMessageStatisticsType,
          waitSeconds: 0,
          sendButtonValue,
          button_radio,
          buttonInputValue
        }
        const sendData = {
          phoneNum,
          content,
          mediaType,
          minNum,
          maxNum,
          sentSilent,
          stopFlag,
          stopFlagSimple,
          uploadExcel,
          groupConcats,
          labelConcats,
          sendButtonValue,
          button_radio,
          buttonInputValue,
          isDeleteMessage,
          sendMessageType: this.sendMessageType,
          dailySendNums: this.dailySendNums
        }
        chrome.storage.local.set({
          confirmSendData: sendData,
          confirmSetData: confirmSetData
        })
      })
    },
    // 为了发送勾选的日志，为了判断用户的点击频率，因此需要在点击change的时候就记录
    notShowAgain(value) {
      let s_status = 1
      if (!value) {
        s_status = 2
      }
      let _This = this
      chrome.storage.local.get('confirmSendData', function (res) {
        let type = res['confirmSendData']['sendMessageType']
        if (type === 'simple') {
          _This.$sendLog(900209, { s_type: 1, s_status })
        }
        if (type === 'pro') {
          _This.$sendLog(900209, { s_type: 2, s_status })
        }
      })
    },
    // statistic里面的调用导出方法
    fnExport(data) {
      const ws = XLSX.utils.json_to_sheet(data)
      ws['!cols'] = [
        { wch: 20 },
        { wch: 20 },
        { wch: 20 },
        { wch: 20 },
        { wch: 20 },
        { wch: 20 },
        { wch: 20 }
      ]
      const wb = XLSX.utils.book_new()
      XLSX.utils.book_append_sheet(wb, ws, 'sheet1')
      XLSX.writeFile(wb, 'Send Result.xlsx')
    },
    // 点击Tab切换的时候执行
    handleClick(tab, event) {
      // sendMessageType
      if (this.activeName === 'send-messages') {
        this.$sendLog(901101)
      }
      if (this.activeName === 'send-messages-pro') {
        this.$sendLog(902101)
      }
      if (this.activeName === 'group-tools') {
        this.$sendLog(903101)
      }
      if (this.activeName === 'strengthen') {
        this.$sendLog(904101)
      }
      if (tab.index === '0') {
        this.sendMessageType = 'simple'
        chrome.storage.local.set({ sendMessageType: this.sendMessageType })
      } else if (tab.index === '1') {
        this.sendMessageType = 'pro'
        chrome.storage.local.set({ sendMessageType: this.sendMessageType })
      }
      if (tab.index === '4') {
        // noinspection UnnecessaryReturnStatementJS
        return
      } else {
        chrome.storage.local.set({ activeName: this.activeName })
      }
      this.setShowStatistics(false)
    },
    // 专门发送消息给到content的方法
    sendDataPopToContent(sendData) {
      chrome.tabs.query({ active: true, currentWindow: true }, function (tabs) {
        chrome.tabs.sendMessage(tabs[0].id, sendData)
      })
    },
    // 安全警示页同意之后调用
    updateShowSafetyWarningFlag() {
      // 1、关闭安全提示页
      this.isShowSafetyWarningFlagValue = false
      // 2、将tab切换到Statistics页面
      // this.activeName = 'statistics'
      this.setShowStatistics(true)
      let _This = this
      // 3、警示页点击同意之后发送日志
      chrome.storage.local.get('confirmSendData', function (res) {
        let type = res['confirmSendData']['sendMessageType']
        if (type === 'simple') {
          _This.$sendLog(900204, { s_type: 1 })
        }
        if (type === 'pro') {
          _This.$sendLog(900204, { s_type: 2 })
        }
      })
      // 4、存储下次是否还展示安全警示页
      if (this.isDontShowSafetyWarning) {
        chrome.storage.local.set({ isShowSafetyWarningFlagValue: false })
      } else {
        chrome.storage.local.set({ isShowSafetyWarningFlagValue: true })
      }

      chrome.storage.local.get(
        [
          'dialogVisible',
          'confirmSetData',
          'confirmSendData',
          'allDataImg',
          'allDataVideo',
          'allDataDocument',
          'audio'
        ],
        (res) => {
          // 如果明确弹窗会有信息
          if (res.dialogVisible !== false) {
            this.dialogVisible = true
          } else {
            // 这块是直接操作去发送
            if (res.confirmSetData) {
              chrome.storage.local.set(res.confirmSetData)
            }
            if (typeof res.confirmSendData === 'object' && res.confirmSendData !== null) {
              // this.updateDisabledSendingFlag(false)
              let confirmSendData = res.confirmSendData
              confirmSendData.allDataImg = res.allDataImg ? res.allDataImg : []
              confirmSendData.allDataVideo = res.allDataVideo ? res.allDataVideo : []
              confirmSendData.allDataDocument = res.allDataDocument ? res.allDataDocument : []
              confirmSendData.audio = res.audio || {}
              this.sendDataPopToContent(confirmSendData)
            }
          }
        }
      )
    },
    sleep(s, e, l) {
      let m = 0
      if (e >= s) {
        m = Math.floor(Math.random() * (e - s + 1) + s) * l
      }
      return new Promise((t) => setTimeout(t, m))
    },
    // 打开或者刷新现有的whatsapp页面
    openOrCheckWaTab(event) {
      chrome.tabs.query({ currentWindow: true }, function (tabs) {
        if (tabs.length > 0) {
          const urlArr = tabs.filter((item) => item.url)
          let whatsappTab = urlArr.find(
            (e) =>
              e.url.startsWith('http://web.whatsapp.com/') ||
              e.url.startsWith('https://web.whatsapp.com/')
          )
          if (typeof whatsappTab !== 'undefined') {
            chrome.tabs.highlight(
              { tabs: whatsappTab.index, windowId: whatsappTab.windowId },
              function () {
                chrome.windows.update(whatsappTab.windowId, { focused: true })
              }
            )
            if (event === 'reload') {
              chrome.tabs.reload(whatsappTab.id)
            }
          } else {
            window.open('https://web.whatsapp.com/')
          }
        }
      })
    },
    messageBoxClose() {
      try {
        this.$msgbox.close()
      } catch (err) {}
    },
    // 更新是否是展示继续的按钮
    updateIsShowContinueBtn(isShow) {
      this.isShowContinueBtn = isShow
      chrome.storage.local.set({ isShowContinueBtn: isShow })
    },
    // 更新禁止发送的状态
    updateDisabledSendingFlag(isDisabled) {
      this.disabledSendingFlag = isDisabled
      chrome.storage.local.set({ disabledSendingFlag: isDisabled })
    },
    // 打开客服并且发送消息
    supportUs() {
      chrome.tabs.query({ active: true, currentWindow: true }, function (tabs) {
        chrome.storage.local.get(['zbaseConfig'], (res) => {
          let serviceUsSrc =
            res.zbaseConfig?.data?.config?.find((item) => item.name === 'servicePhone')?.params
              ?.src ?? ''
          let servicePhoneConf =
            res.zbaseConfig?.data?.config?.find((item) => item.name === 'servicePhone')?.params
              ?.phone ?? '+8617611594206'
          if (serviceUsSrc) {
            window.open(serviceUsSrc)
          } else {
            chrome.tabs.sendMessage(tabs[0].id, { openChat: servicePhoneConf })
          }
        })
      })
    },
    // 打开安全警示页调用
    openSafetyDialog() {
      let _This = this
      chrome.storage.local.get('confirmSendData', function (res) {
        let type = res['confirmSendData']['sendMessageType']
        if (type === 'simple') {
          _This.$sendLog(900202, { s_type: 1 })
        }
        if (type === 'pro') {
          _This.$sendLog(900202, { s_type: 2 })
        }
      })
    },
    // 关闭安全警示页调用
    closeSafetyDialog() {
      let _This = this
      chrome.storage.local.get('confirmSendData', function (res) {
        let type = res['confirmSendData']['sendMessageType']
        if (type === 'simple') {
          _This.$sendLog(900203, { s_type: 1 })
        }
        if (type === 'pro') {
          _This.$sendLog(900203, { s_type: 2 })
        }
      })
    },
    // 安全警示页下次是否还要打开的选择
    againCheckBoxChange(value) {
      let s_status = 1
      if (!value) {
        s_status = 2
      }
      let _This = this
      chrome.storage.local.get('confirmSendData', function (res) {
        let type = res['confirmSendData']['sendMessageType']
        if (type === 'simple') {
          _This.$sendLog(900205, { s_type: 1, s_status })
        }
        if (type === 'pro') {
          _This.$sendLog(900205, { s_type: 2, s_status })
        }
      })
    }
  },
  created: async function () {
    console.log('🔥 [CREATED] Iniciando...')
    console.log('🔥 [CREATED] permissionCode inicial:', this.permissionCode)
    try {
      window.MIGRATION_SIMPLE_FLOW = MIGRATION_SIMPLE_FLOW
    } catch (error) {
      console.error('[MIG] set window MIGRATION flag failed', error)
    }
    try {
      if (chrome && chrome.storage && chrome.storage.local && chrome.storage.local.set) {
        const maybePromise = chrome.storage.local.set({ MIGRATION_SIMPLE_FLOW })
        if (maybePromise && typeof maybePromise.catch === 'function') {
          maybePromise.catch(() => {})
        }
      }
    } catch (error) {
      console.error('[MIG] persist MIGRATION flag from popup failed', error)
    }
    await this.restoreProStatus()
codex/document-lifecycle-of-popup-component-39qnol
    console.log('🔥 [CREATED] Finalizou!')
    console.log('🔥 [CREATED] permissionCode final:', this.permissionCode)
    console.log('🔥 [CREATED] permissionText:', this.permissionText)
main
    let jsPath = '/js/inject/obfuscate.js'
    let temp = document.createElement('script')
    temp.setAttribute('type', 'text/javascript')
    temp.setAttribute('id', 'inject')
    temp.src = chrome.runtime.getURL(jsPath)
    temp.onload = function () {
      //放在页面不好看，执行完后移除掉
      this.parentNode.removeChild(this)
    }
    document.head.appendChild(temp)
    let _This = this
    await new Promise((resolve) => {
      chrome.storage.local.get(
        ['BulkSender_isGuide', 'BulkSender_btnMsg', 'permissionInfo', 'paid_mark', 'myapp_activation'],
        function (res) {
          _This.paidMark = !!res.paid_mark
          _This.myappActivation = !!res.myapp_activation
          _This.permissionInfo = res.permissionInfo || null
          const shouldMarkPro =
            _This.paidMark === true ||
            _This.myappActivation === true ||
            (_This.permissionInfo && _This.permissionInfo.status === 'active')
          if (!_This.permissionCode && shouldMarkPro) {
            _This.permissionCode = 'supabase_pro'
          } else if (!shouldMarkPro && _This.permissionCode === 'supabase_pro') {
            _This.permissionCode = ''
          }
          // 获取权限信息
          if (_This.permissionInfo && 'transaction_id' in _This.permissionInfo) {
            const currentTimestamp = parseInt(new Date().setDate(new Date().getDate() - 1) / 1000)
            if (
              currentTimestamp < _This.permissionInfo['expiration_time'] &&
              _This.permissionInfo['pay_status'] !== 1
            ) {
              _This.permissionCode = _This.permissionInfo['plink_id']
            }
          }
          if (res.BulkSender_isGuide === false) {
            _This.isGuide = false
            if (res.BulkSender_btnMsg === false) {
              _This.buttonMsgDialogVisible = false
            } else {
              _This.buttonMsgDialogVisible = true
            }
          } else {
            _This.isGuide = true
          }
          resolve('')
          _This.grantTrialPermission()
        }
      )
    })

    this.browserType = getBrowser()['browser']
    chrome.tabs.query({ active: true, currentWindow: true }, function (tab) {
      // 查询当前链接是不是web.whatsapp.com，如果不是那么就切换到whatsapp的web页面
      let tabLink = tab[0].url
      let re = /^(http|https):\/\/web.whatsapp.com\/\S*/
      if (!re.test(tabLink)) {
        _This.openOrCheckWaTab()
      }
    })

    // 获取是否展示未激活、未续订的提示
    chrome.storage.local.get(
      ['isShowNoActive', 'isShowNoSubscription', 'isOneNoSubscription'],
      function (res) {
        // console.log("app created chrome.storage.local res", res,new Date().toString())
        if (res.isShowNoActive) {
          _This.isShowNoActive = true
        }
        if (res.isShowNoSubscription) {
          _This.isShowNoSubscription = true
        }
        if (res.isOneNoSubscription) {
          _This.isOneNoSubscription = true
        }
      }
    )

    // popup 没有打开的时候，无法通过chrome.runtime.onMessage获取到消息，所以需要在这里获取一次
    chrome.storage.local.get(['groupsInfo'], function (res) {
      let newGroupOptions = []
      let newGroupsMap = {}
      if (res.groupsInfo !== undefined) {
        res.groupsInfo.forEach((item, i) => {
          newGroupOptions.push({
            data: item.data,
            key: item.key,
            value: item.key,
            label: item.label
          })
          newGroupsMap[item.key] = item.data
        })
        chrome.storage.local.set({ groupOptions: newGroupOptions })
        chrome.storage.local.set({ groupsMap: newGroupsMap })
      }
      _This.groupOptions = newGroupOptions
      _This.groupsMap = newGroupsMap
    })

    chrome.storage.local.get(
      [
        'isBusiness',
        'labelOptions',
        'allDataImg',
        'allDataVideo',
        'allDataDocument',
        'sendMessageFlag',
        'confirmSendData',
        'activeName',
        'firstSendTime',
        'userPhoneNum',
        'installEventFlag',
        'countAll',
        'countTotal',
        'deduplicated',
        'countSuccess',
        'countFail',
        'waitSeconds',
        'chooseWarning',
        'beRemovedWarning',
        'dialogVisible',
        'dailySendNums',
        'sendMessageType',
        'phoneNumList',
        'disabledSendingFlag',
        'isShowContinueBtn'
      ],
      async function (res) {
        if (res.sendMessageFlag === false) {
          // _This.updateDisabledSendingFlag(false)
          let confirmSendData = res.confirmSendData
          confirmSendData.allDataImg = res.allDataImg ? res.allDataImg : []
          confirmSendData.allDataVideo = res.allDataVideo ? res.allDataVideo : []
          confirmSendData.allDataDocument = res.allDataDocument ? res.allDataDocument : []
          _This.sendDataPopToContent(confirmSendData)
          chrome.storage.local.set({ sendMessageFlag: true })
        }
        // activeName是默认的或者storage里面指定 的
        _This.activeName = res.activeName || 'send-messages'
        // 设置首次发送事件的默认值
        _This.firstSendTime = res.firstSendTime || ''
        // 点击popup的事件发送
        _This.$sendLog(900001)
        // 当用户的userPhoneNum是空的时候
        if (res.userPhoneNum === '') {
          _This.firstLoadValue = true
          // todo 这是给到了一个弹窗？？？下面弹窗已经给了呀～
          _This.$alert({})
          _This
            .$confirm(
              '<p>Failed to connect to your WhatsApp, please refresh the page to reconnect.</p><br>' +
                '<p>Otherwise, Bulk Sender will not work.</p>',
              '',
              {
                customClass: 'firstLoad',
                showConfirmButton: true,
                confirmButtonText: 'Reconnect',
                showClose: false,
                showCancelButton: false,
                dangerouslyUseHTMLString: true,
                type: 'warning'
              }
            )
            .then(() => {
              _This.openOrCheckWaTab('reload')
            })
        } else {
          // 即使现在已经有用户手机号了，也要和content通讯一下，为了保证wapi加载成功了
          chrome.tabs.query({ active: true, currentWindow: true }, function (tabs) {
            chrome.tabs.sendMessage(tabs[0].id, { msgFlag: true }, (res) => {
              if (!res) {
                _This.$sendLog(900009)
                _This
                  .$confirm(
                    '<p>Failed to connect to your WhatsApp, please refresh the page to reconnect.</p><br>' +
                      '<p>Otherwise, Bulk Sender will not work.</p>',
                    '',
                    {
                      customClass: 'firstLoad',
                      showConfirmButton: true,
                      confirmButtonText: 'Reconnect',
                      showClose: false,
                      showCancelButton: false,
                      dangerouslyUseHTMLString: true,
                      type: 'warning'
                    }
                  )
                  .then(() => {
                    _This.openOrCheckWaTab('reload')
                  })
              }
            })
          })
          // 如果storage里面没有installEventFlag， 那么我们需要设置一下，并且存入浏览器的数据，这个和content的初始化过程一样～
          if (!res.installEventFlag) {
            const browserData = {
              browserInfo: getBrowser(),
              platform: window.navigator.platform,
              language: window.navigator.language,
              phoneNum: res.userPhoneNum
            }
            chrome.storage.local.set({
              installEventFlag: true,
              ...browserData
            })
            // todo 在这块才记录用户的安装事件？
            _This.$sendLog(900005, browserData)
          }
        }
        _This.sendDataPopToContent({ queryAllGroups: true })
        _This.countAll = res.countAll ? res.countAll : 0
        _This.isBusiness = res.isBusiness ? res.isBusiness : 0
        _This.countTotal = res.countTotal ? res.countTotal : 0
        _This.deduplicated = res.deduplicated ? res.deduplicated : 0
        _This.countSuccess = res.countSuccess ? res.countSuccess : 0
        _This.countFail = res.countFail ? res.countFail : 0
        _This.waitSeconds = res.waitSeconds ? res.waitSeconds : 0
        _This.chooseWarning = res.chooseWarning ? res.chooseWarning : false
        _This.beRemovedWarning = res.beRemovedWarning ? res.beRemovedWarning : false
        _This.labelOptions = res.labelOptions !== undefined ? res.labelOptions : []
        _This.dialogVisible = res.dialogVisible !== undefined ? res.dialogVisible : false
        _This.dailySendNums = res.dailySendNums !== undefined ? res.dailySendNums : {}
        _This.sendMessageType = res.sendMessageType !== undefined ? res.sendMessageType : 'simple'
        _This.phoneNumList = res.phoneNumList !== undefined ? res.phoneNumList : []
        _This.disabledSendingFlag =
          res.disabledSendingFlag !== undefined ? res.disabledSendingFlag : false
        _This.isShowContinueBtn =
          res.isShowContinueBtn !== undefined ? res.isShowContinueBtn : false
        // 如果每日的发送数量这个字段有
        // if(res.dailySendNums){
        //   // 检查每日发送的消息数量
        //   const {isReachSendLimit, showConfirmTitle} = _This.$checkDailySendNums(res.dailySendNums);
        //   // todo 如果到达限制，直接给到限制弹窗?dailySendNums是不是只有发送了消息才能有呢？
        //   if (isReachSendLimit) {
        //     _This.$confirm(showConfirmTitle, "", {
        //       confirmButtonText: "Continue",
        //       cancelButtonText: "Cancel",
        //       type: "warning",
        //       showClose: false,
        //       customClass: "daily-msg-send-warning",
        //       dangerouslyUseHTMLString: true
        //     }).then(() => {
        //       // 到达限制之后，需要给content发送消息
        //       chrome.tabs.query({active: true, currentWindow: true}, function (tabs) {
        //         chrome.tabs.sendMessage(tabs[0].id, {limit_continue:true});
        //       });
        //     });
        //     // todo 定时之后，不知道这个limit_popup是什么？为什么要去添加点击
        //     setTimeout(function () {
        //       document.getElementsByClassName('limit_popup')[0].addEventListener('click',function () {
        //         window.open(LIMIT_POPUP_CRM)
        //       })
        //     },200)
        //   }
        // }
      }
    )
  },
  watch: {
    pauseAndSendBtn: {
      handler(newData, oldData) {
        if (newData.countAll === newData.countSuccess + newData.countFail && newData.countAll > 0) {
          this.updateIsShowContinueBtn(true)
          return
        }
        if (newData.countAll === newData.countSuccess && newData.countAll === 0) {
          this.updateIsShowContinueBtn(false)
          return
        }
        if (newData.countAll > newData.countSuccess) {
          if (
            oldData.countAll === 0 &&
            oldData.countFail === 0 &&
            oldData.countSuccess === 0 &&
            oldData.waitSendNum === 0
          ) {
            if (
              newData.countAll === newData.waitSendNum - newData.countFail &&
              newData.countFail != 0
            ) {
              this.updateIsShowContinueBtn(false)
            } else {
              console.log(newData, oldData)
              let type = this.sendMessageType
              let that = this
              chrome.storage.local.get(['stopFlag', 'stopFlagSimple'], function (res) {
                if ((type === 'pro' && res.stopFlag) || (type === 'simple' && res.stopFlagSimple)) {
                  that.updateIsShowContinueBtn(true)
                } else {
                  that.updateIsShowContinueBtn(false)
                }
              })
            }
          } else {
            let that = this
            if (newData.countAll > newData.countFail + newData.countSuccess) {
              let type = this.sendMessageType
              chrome.storage.local.get(['stopFlag', 'stopFlagSimple'], function (res) {
                if ((type === 'pro' && res.stopFlag) || (type === 'simple' && res.stopFlagSimple)) {
                  that.updateIsShowContinueBtn(true)
                } else {
                  that.updateIsShowContinueBtn(false)
                }
              })
            }
          }
          // this.updateIsShowContinueBtn(false)
          return
        }
        this.updateIsShowContinueBtn(true)
      },
      deep: true,
      immediate: true
    },
    disabledSendingFlagData: {
      handler(newValue, oldValue) {
        if (oldValue.countAll === 0 && oldValue.waitSeconds === 0 && newValue.countAll > 0) {
          this.updateDisabledSendingFlag(true)
        }
      },
      immediate: true
    },
    waitSeconds: {
      handler(newValue, oldValue) {
        if (newValue === 0) {
          // this.updateDisabledSendingFlag(true)
          this.messageBoxClose()
        }
        if (oldValue === 0 && newValue > 0) {
          this.updateDisabledSendingFlag(false)
        }
      },
      immediate: true
    }
  },
  beforeDestroy() {
    if (this._storageChangeHandler) {
      chrome.storage.onChanged.removeListener(this._storageChangeHandler)
    }
  },
  async mounted() {
codex/document-lifecycle-of-popup-component-39qnol
    console.log('⚡ [MOUNTED] Iniciando...')
    console.log('⚡ [MOUNTED] permissionCode:', this.permissionCode)
=======
main
    let _This = this
    this._storageChangeHandler = (changes, area) => {
      if (area !== 'local') return
      let touched = false
      if (changes.paid_mark) {
        this.paidMark = !!changes.paid_mark.newValue
        touched = true
      }
      if (changes.myapp_activation) {
        this.myappActivation = !!changes.myapp_activation.newValue
        touched = true
      }
      if (changes.permissionInfo) {
        this.permissionInfo = changes.permissionInfo.newValue || null
        touched = true
      }
      if (touched) {
        const shouldMarkPro =
          this.paidMark === true ||
          this.myappActivation === true ||
          (this.permissionInfo && this.permissionInfo.status === 'active')
        if (!this.permissionCode && shouldMarkPro) {
          this.permissionCode = 'supabase_pro'
        } else if (!shouldMarkPro && this.permissionCode === 'supabase_pro') {
          this.permissionCode = ''
        }
      }
    }
    chrome.storage.onChanged.addListener(this._storageChangeHandler)
    this.$bridge.onMessage(CONTENT_TO_POP_IS_SHOW_NO_ACTIVE, ({ data }) => {
      _This.isShowNoActive = true
    })
    this.$bridge.onMessage(CONTENT_TO_POP_IS_SHOW_NO_SUBSCRIPTION, ({ data }) => {
      _This.isShowNoSubscription = true
    })
    this.$bridge.onMessage(CONTENT_TO_POP_IS_ONE_NO_SUBSCRIPTION, ({ data }) => {
      _This.isOneNoSubscription = true
    })
    this.$bridge.onMessage(CONTENT_TO_POP_SHOW_SAVE_TIME_DIALOG, ({ data }) => {
      _This.isShowSuggestion = true
      _This.maxSleep = data.maxSleep
    })
    this.$bridge.onMessage(CONTENT_TO_POP_GET_PRO_PERMISSION, ({ data }) => {
      chrome.storage.local.get(['BulkSender_btnMsg'], function (res) {
        _This.isGuide = false
        chrome.storage.local.set({
          BulkSender_isGuide: false
        })
        if (res.BulkSender_btnMsg === false) {
          _This.buttonMsgDialogVisible = false
        } else {
          _This.buttonMsgDialogVisible = true
        }
      })
    })
    chrome.runtime.onMessage.addListener(function (message, sender, sendResponse) {
      if (message.stopFlag) {
        _This.$nextTick(() => {
          // console.log(_This.$refs)
          if (_This.$refs.messagePro) {
            _This.$refs.messagePro.stopFlag = message.stopFlag
          }
          if (_This.$refs.Statistics && _This.sendMessageType == 'pro') {
            setTimeout(() => {
              _This.$refs.Statistics.$msgbox.close()
            }, 600)
          }
        })
        chrome.storage.local.set({ stopFlag: true })
      }
      if (message.stopFlagSimple) {
        _This.$nextTick(() => {
          if (_This.$refs.Statistics && _This.sendMessageType == 'simple') {
            setTimeout(() => {
              _This.$refs.Statistics.$msgbox.close()
            }, 600)
          }
          if (_This.$refs.messageSimple) {
            _This.$refs.messageSimple.stopFlagSimple = message.stopFlagSimple
          }
        })
        chrome.storage.local.set({ stopFlagSimple: message.stopFlagSimple })
      }
      if (message.countAll) {
        _This.countAll = message.countAll
      }
      if (message.countTotal) {
        _This.countTotal = message.countTotal
      }
      if (message.deduplicated) {
        _This.deduplicated = message.deduplicated
      }
      if (message.countSuccess) {
        _This.countSuccess = message.countSuccess
      }
      if (message.countFail) {
        _This.countFail = message.countFail
      }
      if (message.waitSeconds !== undefined) {
        _This.waitSeconds = message.waitSeconds
      }
      if (message.chooseWarning !== undefined) {
        _This.chooseWarning = message.chooseWarning
      }
      if (message.beRemovedWarning !== undefined) {
        _This.beRemovedWarning = message.beRemovedWarning
      }
      if (message.isBusiness !== undefined) {
        _This.isBusiness = message.isBusiness
      }
      if (message.labelOptions !== undefined) {
        console.log('收到消息，获取labelOptions')
        console.log(message.labelOptions)
        _This.labelOptions = message.labelOptions
      }

      if (message.groupsInfo !== undefined) {
        let newGroupOptions = []
        let newGroupsMap = {}
        message.groupsInfo.forEach((item, i) => {
          newGroupOptions.push({
            data: item.data,
            key: item.key,
            value: item.key,
            label: item.label
          })
          newGroupsMap[item.key] = item.data
        })
        _This.groupOptions = newGroupOptions
        _This.groupsMap = newGroupsMap
        chrome.storage.local.set({ groupOptions: newGroupOptions })
        chrome.storage.local.set({ groupsMap: newGroupsMap })
      }
      if (message.reloadPopup) {
        window.location.reload()
        _This.updateIsShowContinueBtn(false)
      }
      if (message.isReachSendLimit) {
        // console.log('limit in app')
        // 达到上限
        _This.$sendLog(908303, { s_feature_id: loggerFeatureId.SEND_MESSAGE })
        _This.isShowPayPermissionPopup = true
      }
      if (message.incrDailySendNums !== undefined) {
        _This.dailySendNums = message.incrDailySendNums
      }
      if (message.phoneNumList) {
        _This.phoneNumList = message.phoneNumList
      }
      if (message.disabledSendingFlag !== undefined) {
        _This.updateDisabledSendingFlag(message.disabledSendingFlag)
      }
      sendResponse()
      return true
    })
    // 获取是否需要提醒引流弹窗

    const tab = await chrome.tabs.query({
      active: true,
      currentWindow: true
    })
    const drainage = await sendMessage('P2C_GET_DRAINAGE', {}, `content-script@${tab[0].id}`)
    if (drainage) {
      this.isShowDrainageDialog = true
    }
    this.getLabelOptionsAndGroupOptions()
    console.log('⚡ [MOUNTED] Finalizou!')
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.guideButton {
  background-color: white;
  position: absolute;
  bottom: 20px;
  right: 13px;
  width: 99px;
  height: 30px;
  border: 0;
  font-weight: bold;
  border-radius: 5px;
  cursor: pointer;
}

.guideStep2Button {
  background-color: white;
  position: absolute;
  bottom: 13px;
  right: 23px;
  width: 99px;
  height: 38px;
  border: 0;
  font-weight: bold;
  border-radius: 5px;
  cursor: pointer;
}

.guideSendButton {
  background-color: #1fa855;
  color: white;
  position: absolute;
  bottom: 62px;
  right: 21px;
  width: 110px;
  height: 35px;
  border: 0;
  font-weight: bold;
  border-radius: 5px;
  cursor: pointer;
}

#guidePage {
  width: 585px;
  height: 600px;
}

#guidePage div {
  height: 100%;
}

#app {
  width: 585px;
  height: 600px;
  padding: 10px 10px 0;
  box-sizing: border-box;
}

.tabContainer {
  width: 565px;
  height: 430px;
  overflow: scroll;
}

.el-header,
.el-footer {
  background-color: #881844;
  color: #333;
  text-align: center;
  line-height: 30px;
}

.el-aside {
  background-color: #d3dce6;
  color: #333;
  text-align: center;
  line-height: 40px;
}

.el-main {
  background-color: #e9eef3;
  color: #333;
  text-align: center;
  line-height: 30px;
}

body > .el-container {
  margin-bottom: 40px;
}

.el-container:nth-child(5) .el-aside,
.el-container:nth-child(6) .el-aside {
  line-height: 260px;
}

.el-container:nth-child(7) .el-aside {
  line-height: 320px;
}

.headerTab {
  background-color: rgba(55, 182, 74, 100);
}

.headerTab.el-tabs.el-tabs--top {
  background-color: #37b64a;
  color: #ffffff;
  height: 36px;
}

.headerTab >>> .el-tabs__item {
  padding: 0 16px;
}

.headerTab >>> .el-tabs__nav-wrap::after {
  background-color: rgba(55, 182, 74, 100);
}

.headerTab >>> .el-tabs__nav.is-top {
  display: flex;
  display: -webkit-flex;
  flex-wrap: nowrap;
  justify-content: space-around;
  width: 100%;
}

.headerTab >>> .el-tabs__item.is-top {
  height: 36px;
}

.headerTab >>> .el-tabs__item.is-active {
  color: #37b64a;
  border-bottom: 2px solid white;
}

.headerTab >>> .el-tabs__active-bar.is-top {
  height: 0px;
}

.headerTab >>> .el-tabs__nav-wrap {
  width: 100% !important;
}

.el-tabs__item:focus.is-active.is-focus:not(:active) {
  -webkit-box-shadow: none !important;
  box-shadow: none !important;
}

.downloadExcel >>> .el-link.el-link--default {
  color: rgba(53, 182, 74, 100);
}

.el-button {
  background-color: white;
  color: rgba(54, 182, 74, 100);
}

.el-button:hover {
  background-color: white;
  color: rgba(54, 182, 74, 100);
}

.el-button:focus {
  background-color: white;
  color: rgba(54, 182, 74, 100);
}

.safety-warning {
  background-color: #c9f8cd;
  height: 380px;
}

.safety-warning-header {
  padding: 37px 49px 0 48px;
  /*text-align: center;*/
  vertical-align: middle;
}

.safety-warning-header svg {
  width: 40px;
  height: 40px;
  position: relative;
  float: left;
  margin: auto 0;
  top: 50%;
}

.safety-warning-header span {
  font-size: 28px;
  position: relative;
  float: left;
  margin-left: 15px;
  top: 5px;
  font-weight: revert;
}

.safety-warning-main {
  padding: 0 49px 0 48px;
}

.safety-warning-main > span {
  width: 100%;
  font-size: 18px;
  position: relative;
  float: left;
  text-align: left;
  font-weight: revert;
  font-family: 'Roboto';
}

.safety-warning-footer {
  padding: 0 49px 0 48px;
}

.safety-warning-footer .el-checkbox {
  float: left;
  font-weight: revert;
  color: #333333;
}

.safety-warning-footer .el-button {
  float: right;
}

.grey-background-color {
  background-color: rgba(249, 249, 249, 100);
}

.headerTab >>> .el-tabs__nav-wrap {
  width: 100% !important;
  background-color: #37b64a;
}
</style>

<style>
.exclamation {
  font-size: 18px;
  font-weight: bolder;
  font-style: normal;
  vertical-align: middle;
  color: red;
  margin-bottom: 2px;
}

::-webkit-scrollbar {
  width: 0;
  height: 0;
}

.sendConfirmFooter .el-checkbox__input > .el-checkbox__inner,
.sendConfirmFooter .el-checkbox__input > .el-checkbox__inner:hover {
  border-color: #36b74a;
}

.sendConfirmFooter > .el-checkbox.is-checked .el-checkbox__input.is-checked > .el-checkbox__inner {
  border-color: #36b74a !important;
  background-color: #36b74a !important;
}

.sendConfirmFooter > .el-checkbox > .el-checkbox__label,
.sendConfirmFooter > .el-checkbox.is-checked > .el-checkbox__label {
  color: rgba(147, 145, 145, 100);
}

/* 36b74a */
ul.sendConfirmUl > li > span {
  height: 27px;
  line-height: 27px;
}

ul.sendConfirmUl > li::marker {
  color: #36b74a;
  font-size: 18px;
}

.el-dialog__headerbtn i {
  color: #0f0101 !important;
  font-weight: 900 !important;
}

#countdown {
  position: absolute;
  top: -25px;
  right: -25px;
}

.el-dialog.sendConfirmPop {
  position: relative;
}

.el-dialog.sendConfirmPop > .el-dialog__body {
  padding: 45px 0 35px 40px;
}

i.boldFont {
  font-size: 14px;
  font-weight: bolder;
  font-style: normal;
  color: #0f0101;
}

i.hoverFont {
  font-size: 14px;
  font-weight: bolder;
  font-style: normal;
  color: white;
}

.safetyFooter .el-checkbox__input > .el-checkbox__inner,
.safetyFooter .el-checkbox__input > .el-checkbox__inner:hover {
  border-color: #36b74a;
}

.safetyFooter > .el-checkbox.is-checked .el-checkbox__input.is-checked > .el-checkbox__inner {
  border-color: #36b74a !important;
  background-color: #36b74a !important;
}

.safetyFooter > .el-checkbox > .el-checkbox__label,
.safetyFooter > .el-checkbox.is-checked > .el-checkbox__label {
  color: rgba(147, 145, 145, 100);
}

.el-dialog.el-dialog--center.safetyPop > .el-dialog__footer {
  margin: 6px auto 17px;
  padding-top: 0 !important;
}

.safetyFooter > .el-button.el-button--success:focus,
.safetyFooter > .el-button.el-button--success:hover,
.safetyFooter > .el-button.el-button--success {
  background-color: rgba(55, 182, 75, 100) !important;
  color: rgba(255, 255, 255, 100) !important;
  width: 237px;
  height: 35px;
}

ul.safetyFirstUl,
ul.safetySecondUl {
  margin: 7px 0 0 45px;
  list-style-type: disc;
}

ul.safetyFirstUl > li,
ul.safetySecondUl > li {
  height: 24px;
  vertical-align: middle;
  margin-left: 5px;
}

ul.safetyFirstUl > li::marker {
  color: black;
  font-size: 18px;
}

ul.safetySecondUl > li::marker {
  color: red;
  font-size: 18px;
}

.el-dialog__wrapper.concatsPop ~ .v-modal {
  width: 565px;
  height: 457px;
  top: 89px !important;
  left: 10px !important;
  opacity: 0.2 !important;
}

* {
  margin: 0;
  padding: 0;
}

.common-loading i.el-icon-loading {
  color: rgba(55, 182, 74, 100);
  width: 30px;
  height: 30px;
}

.common-loading i.el-icon-loading::before {
  font-size: 30px;
}

.common-loading p.el-loading-text {
  color: rgba(55, 182, 74, 100);
}

.disableBtn {
  color: #fff !important;
  background-color: #b3e19d !important;
  border-color: #b3e19d !important;
}

.emptyContent {
  padding-bottom: 0 !important;
}

.emptyContent .el-message-box__title {
  padding-left: 20px;
  padding-top: 35px;
}

.emptyContent .el-message-box__status.el-icon-warning {
  color: #c12324;
  padding-right: 30px;
}

.emptyContent .el-message-box__content {
  padding: 20px 15px;
}

.emptyContent .el-icon-close {
  color: #909399 !important;
}

.emptyContent .loading-green-icon {
  padding: 0;
  color: rgba(55, 182, 74, 100);
}

.emptyContent .loading-green-icon {
  padding: 0;
  color: rgba(55, 182, 74, 100) !important;
}

.deleteConfirmHeader .el-message-box__header,
.deleteConfirmHeader .el-message-box__title {
  padding-top: 0 !important;
}

.firstLoad {
  padding-bottom: 0 !important;
}

.firstLoad .el-message-box__title {
  padding-left: 20px;
}

.firstLoad .el-message-box__status.el-icon-warning {
  color: #c12324;
  padding-right: 30px;
  padding-bottom: 63px;
}

.firstLoad .el-message-box__content {
  padding: 20px 15px;
}

.firstLoad .el-icon-close {
  color: #909399 !important;
}

.firstLoad .loading-green-icon {
  padding: 0;
  color: rgba(55, 182, 74, 100);
}

.firstLoad .el-message-box__btns {
  text-align: center;
  padding-bottom: 15px;
}

.firstLoad .el-button {
  left: 252px;
  top: 658px;
  width: 160px;
  height: 40px;
  border-radius: 4px;
  background-color: rgba(54, 182, 74, 100);
  color: rgba(255, 255, 255, 100);
  font-size: 14px;
  text-align: center;
  border: 1px solid rgba(187, 187, 187, 100);
}

.firstLoad .el-button--primary {
  color: rgba(255, 255, 255, 100);
  background-color: rgba(54, 182, 74, 100);
  border: 1px solid rgba(187, 187, 187, 100);
}

.firstLoad .el-button:hover {
  color: rgba(255, 255, 255, 100);
  background-color: rgba(54, 182, 74, 100);
  border: 1px solid rgba(187, 187, 187, 100);
}

.firstLoad .el-button:focus {
  color: rgba(255, 255, 255, 100);
  background-color: rgb(54, 182, 74);
  border: 1px solid rgba(187, 187, 187, 100);
}

.confirmButton {
  background-color: #ffffff !important;
  color: #35b64a !important;
  border-color: #35b64a !important;
}

.el-progress-bar__outer {
  width: 450px;
  margin: 0;
}

.el-progress-bar {
  width: 80% !important;
}

.el-progress__text {
  float: right;
  margin-right: 15px;
}

.el-message .el-icon-warning {
  display: none;
}

.el-message--warning {
  padding-left: 35px !important;
}

.daily-msg-send-warning {
  width: 459px;
  /*height: 243px;*/
}

.daily-msg-send-warning
  .el-message-box__content
  .el-message-box__container
  .el-message-box__status {
  top: 12px;
  color: rgba(53, 182, 74, 100);
}

.daily-msg-send-warning
  > .el-message-box__content
  > .el-message-box__container
  > .el-message-box__message
  > p {
  white-space: pre-wrap;
  font-size: 16px;
  text-align: left;
}

.daily-msg-send-warning > .el-message-box__btns {
  bottom: 0;
  text-align: center;
}

.daily-msg-send-warning > .el-message-box__btns button.el-button {
  background-color: rgba(53, 182, 74, 100);
  border-color: rgba(53, 182, 74, 100);
  color: #ffffff;
  width: 100px;
  height: 40px;
  margin: 15px;
}

.daily-msg-send-warning > .el-message-box__btns button.el-button--primary {
  background-color: rgba(53, 182, 74, 100);
  border-color: rgba(53, 182, 74, 100);
  color: #ffffff;
  width: 100px;
  height: 40px;
  margin: 15px;
}

.inviteCommentMessageBox .el-message-box__header {
  padding-top: 0px !important;
}

.inviteCommentMessageBox .el-message-box__btns {
  text-align: center !important;
  padding-top: 0px !important;
  padding-bottom: 10px !important;
  margin-top: -10px !important;
}

.inviteCommentMessageBoxConfirm {
  font-size: 14px !important;
  background-color: #009f79 !important;
  margin-left: 7px !important;
  padding: 9px 25px !important;
}

.inviteCommentMessageBoxCancel {
  font-size: 14px !important;
  margin-right: 7px !important;
  padding: 9px 25px !important;
}

.openRecommendCommentMessageBox .el-message-box__header {
  padding-top: 0px !important;
}

.openRecommendCommentMessageBox .el-message-box__btns {
  text-align: center !important;
  padding-top: 25px !important;
  padding-bottom: 10px !important;
}

.openRecommendCommentMessageBoxConfirm {
  font-size: 14px !important;
  background-color: #009f79 !important;
  margin-left: 15px !important;
  padding: 9px 20px !important;
}

.openRecommendCommentMessageBoxCancel {
  font-size: 14px !important;
  margin-right: 15px !important;
  padding: 9px 20px !important;
}

.el-card.is-always-shadow,
.el-card.is-hover-shadow:focus,
.el-card.is-hover-shadow:hover {
  box-shadow: 0 0px 0px 0 rgb(0 0 0 / 0%);
}

.emptyContentTemplate .el-message-box__status.el-icon-warning {
  padding-right: 10px !important;
}

.emptyContentTemplate {
  padding-bottom: 0 !important;
}

.emptyContentTemplate .el-message-box__title {
  padding-left: 20px;
  padding-top: 35px;
}

.emptyContentTemplate .el-message-box__status.el-icon-warning {
  color: #c12324;
  padding-right: 30px;
}

.emptyContentTemplate .el-message-box__content {
  padding: 20px 15px;
}

.emptyContentTemplate .el-icon-close {
  color: #909399 !important;
}

.emptyContentTemplate .loading-green-icon {
  padding: 0;
  color: rgba(55, 182, 74, 100);
}

.emptyContentTemplate .loading-green-icon {
  padding: 0;
  color: rgba(55, 182, 74, 100) !important;
}
</style>

<style scoped>
.headerTab {
  background-color: rgba(55, 182, 74, 100);
}

.headerTab.el-tabs.el-tabs--top {
  background-color: #37b64a;
  color: #ffffff;
  height: 36px;
}

.headerTab >>> .el-tabs__item {
  padding: 0 16px;
}

.pt >>> .el-tabs__item {
  overflow: hidden;
}

.headerTab >>> .el-tabs__nav-wrap::after {
  background-color: rgba(55, 182, 74, 100);
}

.headerTab >>> .el-tabs__nav.is-top {
  display: flex;
  display: -webkit-flex;
  flex-wrap: nowrap;
  justify-content: space-around;
  width: 100%;
}

.headerTab >>> .el-tabs__item.is-top {
  height: 36px;
}

.headerTab >>> .el-tabs__item.is-active {
  color: #37b64a;
  border-bottom: 2px solid white;
}

.headerTab >>> .el-tabs__active-bar.is-top {
  height: 0px;
}

.headerTab >>> .el-tabs__nav-wrap {
  width: 100% !important;
}

.el-tabs__item:focus.is-active.is-focus:not(:active) {
  -webkit-box-shadow: none !important;
  box-shadow: none !important;
}

.downloadExcel >>> .el-link.el-link--default {
  color: rgba(53, 182, 74, 100);
}

.el-button {
  background-color: white;
  color: rgba(54, 182, 74, 100);
}

.el-button:hover {
  background-color: white;
  color: rgba(54, 182, 74, 100);
}

.el-button:focus {
  background-color: white;
  color: rgba(54, 182, 74, 100);
}

.safety-warning {
  background-color: #c9f8cd;
  height: 380px;
}

.safety-warning-header {
  padding: 37px 49px 0 48px;
  /*text-align: center;*/
  vertical-align: middle;
}

.safety-warning-header svg {
  width: 40px;
  height: 40px;
  position: relative;
  float: left;
  margin: auto 0;
  top: 50%;
}

.safety-warning-header span {
  font-size: 28px;
  position: relative;
  float: left;
  margin-left: 15px;
  top: 5px;
  font-weight: revert;
}

.safety-warning-main {
  padding: 0 49px 0 48px;
}

.safety-warning-main > span {
  width: 100%;
  font-size: 18px;
  position: relative;
  float: left;
  text-align: left;
  font-weight: revert;
  font-family: 'Roboto';
}

.safety-warning-footer {
  padding: 0 49px 0 48px;
}

.safety-warning-footer .el-checkbox {
  float: left;
  font-weight: revert;
  color: #333333;
}

.safety-warning-footer .el-button {
  float: right;
}

.grey-background-color {
  background-color: rgba(249, 249, 249, 100);
}

.headerTab >>> .el-tabs__nav-wrap {
  width: 100% !important;
  background-color: #37b64a;
}
</style>
