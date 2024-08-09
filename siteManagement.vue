<template>
    <div id="system_account">
      <div ref="queryRef">
        <el-form
          :inline="true"
          :model="queryForm"
          class="search_left"
          label-width="70px"
        >
          <el-form-item
            label=""
            prop="websiteId"
            style="width: 200px"
          >
            <el-input
              v-model="queryForm.websiteId"
              placeholder="请输入站点ID"
              maxlength="32"
            />
          </el-form-item>
          <el-form-item
            label=""
            prop="websiteInnerName"
            style="width: 200px"
          >
            <el-input
              v-model="queryForm.websiteInnerName"
              placeholder="站点内部名称"
              maxlength="128"
            />
          </el-form-item>
          <el-form-item
            label=""
            prop="websiteTitle"
            style="width: 200px"
          >
            <el-input
              v-model="queryForm.websiteTitle"
              placeholder="请输入站点标题"
              maxlength="128"
            />
          </el-form-item>
          <el-form-item
            label=""
            prop="ownerUserId"
            style="width: 200px"
          >
            <RemoteInput
              placeholder="请输入登录账号/用户ID"
              name="userId"
              v-model:remoteId="queryForm.ownerUserId"
            ></RemoteInput>
          </el-form-item>
          <el-form-item
            label=""
            prop="websiteTemplateRef"
            style="width: 200px"
          >
            <el-select
              v-model="addForm.websiteTemplateRef"
              filterable
              remote
              reserve-keyword
              placeholder="请输入引用模版ID"
              :placeholder="queryForm.websiteTemplateRef"
              clearable
              :remote-method="remoTetemplateIdChange"
            >
              <el-option
                v-for="(item, index) in remoteTemplateOptions"
                :key="index"
                :label="item.templateName + ' - ' + item.templateId"
                :value="item.templateId"
              />
            </el-select>
            <!-- <el-input v-model="addForm.websiteTemplateRef" placeholder="请输入引用模版ID" maxlength="32" autocomplete="off" /> -->
          </el-form-item>
          <el-form-item
            label=""
            prop="langCodes"
            style="width: 200px"
          >
            <el-select
              v-model="queryForm.langCodes"
              multiple
              placeholder="请选择语言"
              style="width: 100%"
            >
              <el-option
                v-for="item in optionsLang"
                :key="item.value"
                :label="item.label"
                :value="item.value"
              />
            </el-select>
          </el-form-item>
          <el-form-item
            label=""
            prop="areaCodes"
            style="width: 200px"
          >
            <el-select
              v-model="queryForm.areaCodes"
              multiple
              placeholder="请选择地区"
              style="width: 100%"
            >
              <el-option
                v-for="item in optionsArea"
                :key="item.value"
                :label="item.label"
                :value="item.value"
              />
            </el-select>
          </el-form-item>
          <ExpandableSection
            v-model="isExpanded"
            :resizeRef="queryRef"
          ></ExpandableSection>
          <el-form-item>
            <el-button
              type="primary"
              @click="searchButton({ ...queryForm, ...pageObj })"
              :loading="isLoading"
            >
              <el-icon style="margin: 0 5px 0 0">
                <Search />
              </el-icon>
              查询
            </el-button>
            <el-button
              type="primary"
              @click="resetButton"
              plain
            >
              <el-icon style="margin: 0 5px 0 0">
                <Refresh />
              </el-icon>
              重置
            </el-button>
            <el-button
              type="success"
              @click="addButton"
            >
              <el-icon style="margin: 0 5px 0 0">
                <Plus />
              </el-icon>
              新增
            </el-button>
          </el-form-item>
        </el-form>
      </div>
      <!-- 表格 -->
      <div class="account-table">
        <el-table
          v-loading="isLoading"
          stripe
          highlight-current-row
          :show-overflow-tooltip="{ disabled: true }"
          :data="tableList"
          border
          style="width: 100%"
          :height="count(queryRef)"
        >
          <el-table-column
            label="站点内部名称/ID"
            key="websiteId"
            prop="websiteId"
            width="200"
          >
            <template #="{ row }">
              <p>{{ row.websiteInnerName }}</p>
              <p>
                <el-link
                  class="like-a"
                  type="primary"
                  @click="handleEdit(row)"
                >
                  {{ row.websiteId }}
                </el-link>
                <copy :content="row.websiteId" />
              </p>
            </template>
          </el-table-column>
          <el-table-column
            label="所属用户昵称/ID"
            key="planTitle"
            prop="planTitle"
            width="190"
          >
            <template #="{ row }">
              <p>{{ row.userName }}</p>
              <p>{{ row.ownerUserId }}</p>
            </template>
          </el-table-column>
          <el-table-column
            label="创建时间"
            key="createTime"
            prop="createTime"
            width="170"
          >
            <template #="{ row }">
              {{ unitFormatDate(row.createTime) }}
            </template>
          </el-table-column>
          <el-table-column
            label="站点标题"
            key="websiteTitle"
            prop="websiteTitle"
            width="120"
          />
          <el-table-column
            label="关键字"
            key="keyWords"
            prop="keyWords"
          />
          <el-table-column
            label="站点描述"
            key="describe"
            prop="describe"
            :show-overflow-tooltip="{ disabled: true }"
            width="170"
          />
          <el-table-column
            label="在线客服代码"
            key="onlineServiceCode"
            prop="onlineServiceCode"
          />
          <el-table-column
            label="统计代码"
            key="hasStatsCode"
            prop="hasStatsCode"
            width="60"
          >
            <template #="{ row }">
              <el-text
                v-if="row.hasStatsCode == 'y'"
                class="mx-1"
                type="primary"
              >
                <el-link
                  @click="handTabs(row)"
                  type="primary"
                >
                  查看
                </el-link>
              </el-text>
              <el-text
                v-else
                class="mx-1"
                type="info"
              >
                -
              </el-text>
            </template>
          </el-table-column>
          <!-- <el-table-column label="统计代码" align="left" key="hasStatsCode" prop="hasStatsCode" /> -->
          <el-table-column
            label="语言"
            key="langCodes"
            prop="langCodes"
          >
            <template #="{ row }">{{ formatLanguageData(row.langCodes) }}</template>
          </el-table-column>
          <el-table-column
            label="地区"
            key="areaCodes"
            prop="areaCodes"
          >
            <template #="{ row }">{{ formatAreaData(row.areaCodes) }}</template>
          </el-table-column>
          <el-table-column
            label="引用模版名称/ID"
            key=""
            prop=""
            width="190"
          >
            <template #="{ row }">
              <p>{{ row.templateName }}</p>
              <p>{{ row.websiteTemplateRef }}</p>
            </template>
          </el-table-column>
          <el-table-column
            label="状态"
            key="websiteStatus"
            prop="websiteStatus"
            width="70"
          >
            <template #="{ row }">
              <el-dropdown
                trigger="click"
                size="small"
                @command="stateChange"
              >
                <span class="el-dropdown-link">
                  <el-tag
                    v-if="row.websiteStatus == 'y'"
                    class="mx-1"
                    type="primary"
                  >
                    启用
                  </el-tag>
                  <el-tag
                    v-else-if="row.websiteStatus == 'm'"
                    class="mx-1"
                    type="warning"
                  >
                    维护
                  </el-tag>
                  <el-tag
                    v-else
                    class="mx-1"
                    type="info"
                  >
                    关闭
                  </el-tag>
                </span>
                <template #dropdown>
                  <el-dropdown-menu>
                    <el-dropdown-item
                      class="mx-1"
                      :command="{ row, websiteStatus: 'y' }"
                    >
                      <el-tag type="primary">启用</el-tag>
                    </el-dropdown-item>
                    <el-dropdown-item
                      class="mx-1"
                      :command="{ row, websiteStatus: 'm' }"
                    >
                      <el-tag type="warning">维护</el-tag>
                    </el-dropdown-item>
                    <el-dropdown-item
                      class="mx-1"
                      :command="{ row, websiteStatus: 'c' }"
                    >
                      <el-tag type="info">关闭</el-tag>
                    </el-dropdown-item>
                  </el-dropdown-menu>
                </template>
              </el-dropdown>
              <!-- <el-text v-if="row.websiteStatus == 'y'" class="mx-1" type="primary">启用</el-text>
              <el-text v-else-if="row.websiteStatus == 'm'" class="mx-1" type="warning">维护</el-text>
              <el-text v-else class="mx-1" type="info">关闭</el-text> -->
              <!-- <el-dropdown size="small" split-button #="{ row }"></el-dropdown>>
                <el-text v-if="row.websiteStatus == 'y'" class="mx-1" type="primary">启用</el-text>
                <el-text v-else-if="row.websiteStatus == 'm'" class="mx-1" type="warning">维护</el-text>
                <el-text v-else class="mx-1" type="info">关闭</el-text>
                <el-dropdown-menu slot="dropdown">
                  <el-dropdown-item>启用</el-dropdown-item>
                  <el-dropdown-item>维护</el-dropdown-item>
                  <el-dropdown-item>关闭</el-dropdown-item>
                </el-dropdown-menu>
              </el-dropdown> -->
            </template>
          </el-table-column>
          <el-table-column
            label="备注"
            key="memo"
            prop="memo"
            width="170"
          >
            <template #="{ row }">
              {{ row.memo }}
              <!-- <span @click="memoInfoTabs(row)">{{ row.memo }}</span> -->
            </template>
          </el-table-column>
          <!-- <el-table-column :label="t('table.controls')" width="80" fixed="right">
            <template #="{ row }">
              <el-button type="primary" size="small" plain @click="handleEdit(row)">编辑</el-button>
            </template>
          </el-table-column> -->
        </el-table>
      </div>
      <div class="pagination">
        <el-pagination
          v-model:current-page="pageObj.page"
          v-model:page-size="pageObj.size"
          :page-sizes="[15, 30, 50, 100]"
          layout="prev, pager, next, jumper, ->, sizes, total"
          :total="total"
          @size-change="handleSizeChange"
          @current-change="handleCurrentChange"
        />
      </div>
      <!-- 新增/编辑 站点弹出框 -->
      <el-dialog
        v-model="isForm"
        title="站点"
        width="800"
        top="5vh"
        :close-on-click-modal="false"
        @close="closeDialog"
      >
        <el-form
          :model="addForm"
          ref="formRef"
          :rules="rulesAddFrom"
          style="width: 600px; margin: 0 auto"
          label-width="130"
        >
          <el-form-item
            label="站点ID："
            prop="websiteId"
            v-if="distinguish"
          >
            <p>
              {{ addForm.websiteId }}
              <copy :content="addForm.websiteId" />
            </p>
          </el-form-item>
          <el-form-item
            label="用户ID："
            prop="ownerUserId"
          >
            <RemoteInput
              placeholder="请输入用户ID"
              name="userId"
              v-model:remoteId="addForm.ownerUserId"
            ></RemoteInput>
          </el-form-item>
          <el-form-item
            label="站点内部名称："
            prop="websiteInnerName"
          >
            <el-input
              v-model="addForm.websiteInnerName"
              placeholder="请输入站点内部名称"
              maxlength="128"
              autocomplete="off"
            />
          </el-form-item>
          <el-form-item
            label="引用模版ID："
            prop="websiteTemplateRef"
          >
            <el-select
              v-model="addForm.websiteTemplateRef"
              filterable
              remote
              reserve-keyword
              placeholder="请输入引用模版ID"
              :placeholder="addForm.websiteTemplateRef"
              clearable
              :remote-method="remoTetemplateIdChange"
            >
              <el-option
                v-for="(item, index) in remoteTemplateOptions"
                :key="index"
                :label="item.templateName + ' - ' + item.templateId"
                :value="item.templateId"
              />
            </el-select>
            <!-- <el-input v-model="addForm.websiteTemplateRef" placeholder="请输入引用模版ID" maxlength="32" autocomplete="off" /> -->
          </el-form-item>
          <el-form-item
            label="网站标题："
            prop="websiteTitle"
          >
            <el-input
              v-model="addForm.websiteTitle"
              placeholder="请输入网站标题"
              maxlength="128"
              autocomplete="off"
            />
          </el-form-item>
          <el-form-item
            label="站点描述："
            prop="describe"
          >
            <el-input
              type="textarea"
              show-word-limit
              placeholder="请输入站点描述"
              :rows="2"
              v-model="addForm.describe"
              maxlength="255"
              autocomplete="off"
            />
          </el-form-item>
          <el-form-item
            label="网站状态："
            prop="websiteStatus"
          >
            <el-radio-group v-model="addForm.websiteStatus">
              <el-radio value="y">启用</el-radio>
              <el-radio value="m">维护</el-radio>
              <el-radio
                value="c"
                v-if="distinguish"
              >
                关闭
              </el-radio>
            </el-radio-group>
          </el-form-item>
          <el-form-item
            label="关键字："
            prop="keyWords"
          >
            <el-input
              v-model="addForm.keyWords"
              placeholder="请输入关键字"
              autocomplete="off"
            />
          </el-form-item>
          <el-form-item
            label="统计代码："
            prop="hasStatsCode"
          >
            <el-radio-group v-model="addForm.websiteStatus">
              <el-radio value="y">启用</el-radio>
              <el-radio value="m">不启用</el-radio>
            </el-radio-group>
          </el-form-item>
          <el-form-item
            label="语言列表："
            prop="langCodes"
          >
            <el-select
              v-model="addForm.langCodes"
              multiple
              placeholder="请选择语言"
            >
              <el-option
                v-for="item in optionsLang"
                :key="item.value"
                :label="item.label"
                :value="item.value"
              />
            </el-select>
          </el-form-item>
          <el-form-item
            label="地区列表："
            prop="areaCodes"
          >
            <el-select
              v-model="addForm.areaCodes"
              multiple
              placeholder="请选择地区"
            >
              <el-option
                v-for="item in optionsArea"
                :key="item.value"
                :label="item.label"
                :value="item.value"
              />
            </el-select>
          </el-form-item>
          <el-form-item
            label="在线客服代码："
            prop="onlineServiceCode"
          >
            <el-input
              type="textarea"
              show-word-limit
              placeholder="请输入在线客服代码"
              :rows="2"
              v-model="addForm.onlineServiceCode"
              maxlength="255"
              autocomplete="off"
            />
          </el-form-item>
          <el-form-item
            label="备注："
            prop="memo"
          >
            <el-input
              type="textarea"
              show-word-limit
              placeholder="请输入上级备注"
              :rows="2"
              v-model="addForm.memo"
              maxlength="255"
              autocomplete="off"
            />
          </el-form-item>
        </el-form>
        <template #footer>
          <div class="dialog-footer">
            <el-button @click="closeDialog">取消</el-button>
            <el-button
              type="primary"
              :loading="isLoading2"
              @click="submitInfo(formRef)"
            >
              提交
            </el-button>
          </div>
        </template>
      </el-dialog>
      <!-- 统计代码tab页面 -->
      <el-dialog
        v-model="isTabs"
        title="统计代码"
        width="400"
        :close-on-click-modal="false"
      >
        <p>
          初始化代码: {{ stateInfo.statsCodeInit }}
          <copy :content="stateInfo.statsCodeInit"></copy>
        </p>
        <p>
          统计代码URL: {{ stateInfo.statsCodeJsUrl }}
          <copy :content="stateInfo.statsCodeJsUrl"></copy>
        </p>
      </el-dialog>
      <!-- 备注tab页面 -->
      <el-dialog
        v-model="isMemoTab"
        title="备注"
        width="400"
        :close-on-click-modal="false"
      >
        <p>{{ memoInfo }}</p>
      </el-dialog>
    </div>
  </template>
  
  <script setup lang="ts">
  import i18n from '@/locales'
  const { t } = i18n.global as any
  import { setPermissionsStore } from '@/stores/index'
  import {
    reqGetALLWebsiteList,
    reqWebsiteCodeDelete,
    reqWebsiteCodeAdd,
    reqWebsiteCodeEdit,
    reqListBlurTemplate
  } from '@/api/site/index'
  import RemoteInput from '@/components/remoteInput/index.vue'
  // 计算表格高度
  import count from '@/utils/tableHeight'
  import { ElMessageBox, rowContextKey } from 'element-plus'
  import TimeSelect from '@/components/timeSelect/index.vue'
  import { useOptions, optionsLang, optionsArea } from '@/const/options'
  import { unitFormatDate } from '@/utils/dateFormat/index'
  import ExpandableSection from '@/components/expandIcon/index.vue'
  
  const tableRef = ref()
  const queryRef = ref()
  const permissionStore = setPermissionsStore()
  const per = computed(() => permissionStore.menupermissions)
  const isExpanded = ref(false)
  console.log(per, 'per')
  const { formatData: formatLanguageData } = useOptions(optionsLang)
  const { formatData: formatAreaData } = useOptions(optionsArea)
  
  const rulesAddFrom = ref({
    ownerUserId: [{ required: true, message: '请输入用户ID', trigger: 'blur' }],
    websiteInnerName: [{ required: true, message: '请输入内部名称', trigger: 'blur' }],
    websiteTitle: [{ required: true, message: '请输入站点标题', trigger: 'blur' }],
    websiteTemplateRef: [{ required: true, message: '请输入模版ID', trigger: 'blur' }]
  })
  
  const initQueryForm = () => ({
    websiteId: '',
    websiteInnerName: '',
    websiteTitle: '',
    describe: '',
    ownerUserId: '',
    websiteTemplateRef: '',
    langCodes: [],
    areaCodes: [],
    sortOrder: 'DESC',
    sortName: 'create_time'
  })
  
  const initAddForm = () => ({
    websiteId: '',
    ownerUserId: '',
    websiteInnerName: '',
    websiteTitle: '',
    describe: '',
    keyWords: '',
    onlineServiceCode: '',
    hasStatsCode: 'y',
    websiteTemplateRef: '',
    websiteStatus: 'y',
    langCodes: [],
    areaCodes: [],
    memo: ''
  })
  
  const initStateInfo = () => ({
    websiteStatus: '',
    statsCodeInit: '',
    statsCodeJsUrl: ''
  })
  
  const initPageObj = () => ({
    page: 1,
    size: 15
  })
  
  //区分点击的是新增账号还是编辑账号   如果为真 则是编辑
  const distinguish = ref(false)
  //分页数据
  const pageObj = ref(initPageObj())
  const isForm = ref(false)
  const isEditForm = ref(false)
  const isTabs = ref(false)
  const isMemoTab = ref(false)
  const isLoading = ref(false)
  const isLoading2 = ref(false)
  const total = ref(0)
  const formRef: any = ref()
  const tableList: any = ref([])
  const addForm = ref(initAddForm())
  //const editForm = ref(initEditForm());
  const queryForm = ref(initQueryForm())
  const stateInfo = ref(initStateInfo())
  const memoInfo = ref()
  const remoteTemplateOptions: any = ref([])
  
  async function searchButton(vals = { ...queryForm.value, ...initPageObj() }) {
    isLoading.value = true
    try {
      let res = await reqGetALLWebsiteList(vals)
      tableList.value = res.data.list
      total.value = Number(res.data.total)
      if (res.success) {
      } else {
        ElMessage({
          showClose: true,
          message: res.errMessage,
          type: 'error'
        })
      }
    } catch (error) {
    } finally {
      isLoading.value = false
    }
  }
  
  const resetButton = () => {
    Object.assign(queryForm.value, initQueryForm())
    searchButton({ ...queryForm.value, ...pageObj.value })
  }
  
  const addButton = () => {
    isForm.value = true
    distinguish.value = false
  }
  
  const handleEdit = (row) => {
    let { createTime, userName, statsCodeJsUrl, statsCodeInit, ...rest } = row
    isForm.value = true
    distinguish.value = true
    Object.assign(addForm.value, { ...rest })
  }
  
  const stateChange = (row) => {
    let { createTime, userName, statsCodeJsUrl, statsCodeInit, ...rest } = row.row
    Object.assign(addForm.value, { ...rest })
    addForm.value.websiteStatus = row.websiteStatus
    if (addForm.value.websiteStatus == 'c') {
      ElMessageBox.confirm(t('pop.areyousurepage'), t('pop.warn'), {
        confirmButtonText: t('button.determine'),
        cancelButtonClass: t('button.cancel'),
        type: 'warning'
      }).then(() => {
        editFormInfo()
      })
    } else {
      editFormInfo()
    }
  }
  
  const submitInfo = (formRefval) => {
    isLoading2.value = true
    //验证表单信息
    formRefval.validate((valid, fields) => {
      if (valid) {
        formRefval.validate(async (valid, fields) => {
          if (valid) {
            if (distinguish.value) {
              editFormInfo()
            } else {
              addFormInfo()
            }
          } else {
            console.log('error submit!', fields)
            isLoading2.value = false
          }
        })
      } else {
        console.log('error submit!', fields)
        isLoading2.value = false
      }
    })
  }
  
  const addFormInfo = async () => {
    try {
      let res = await reqWebsiteCodeAdd({ ...addForm.value })
      if (res.success) {
        ElMessage({
          showClose: true,
          message: t('table.controls') + t('errorCode.0'),
          type: 'success'
        })
        searchButton({
          ...Object.assign(queryForm.value, initQueryForm()),
          ...Object.assign(pageObj.value)
          // ...Object.assign(pageObj.value, initPageObj())
        })
        isForm.value = false
        Object.assign(addForm.value, initAddForm())
      } else {
        ElMessage({
          showClose: true,
          message: res.errMessage,
          type: 'error'
        })
      }
    } catch (err) {
      console.log(err)
    } finally {
      isLoading2.value = false
      // Object.assign(addForm.value, initAddForm())
    }
  }
  
  const editFormInfo = async () => {
    try {
      let res = await reqWebsiteCodeEdit({ ...addForm.value })
      if (res.success) {
        ElMessage({
          showClose: true,
          message: t('table.controls') + t('errorCode.0'),
          type: 'success'
        })
        searchButton({
          ...Object.assign(queryForm.value, initQueryForm()),
          ...Object.assign(pageObj.value)
          // ...Object.assign(pageObj.value, initPageObj())
        })
        isForm.value = false
        Object.assign(addForm.value, initAddForm())
      } else {
        ElMessage({
          showClose: true,
          message: res.errMessage,
          type: 'error'
        })
      }
    } catch (err) {
      console.log(err)
    } finally {
      isLoading2.value = false
      // Object.assign(addForm.value, initAddForm())
    }
  }
  
  const handleSizeChange = (val) => {
    pageObj.value.size = val
    searchButton({ ...queryForm.value, ...pageObj.value })
  }
  
  const handleCurrentChange = (val) => {
    pageObj.value.page = val
    searchButton({ ...queryForm.value, ...pageObj.value })
  }
  
  async function remoTetemplateIdChange(val) {
    if (val) {
      try {
        let res = await reqListBlurTemplate({ templateId: val })
        remoteTemplateOptions.value = res.data
      } catch (err) {}
    } else {
      remoteTemplateOptions.value = []
    }
  }
  
  const cancelEvent = () => {}
  
  const closeDialog = () => {
    isForm.value = false
    isEditForm.value = false
    isTabs.value = false
    distinguish.value = false
    isMemoTab.value = false
    Object.assign(addForm.value, initAddForm())
    // Object.assign(editForm.value, initEditForm());
    formRef.value.clearValidate()
  }
  
  const handTabs = (row) => {
    isTabs.value = true
    let { createTime, userName, ...rest } = row
    Object.assign(stateInfo.value, { ...rest })
  }
  
  const memoInfoTabs = (row) => {
    isMemoTab.value = true
    memoInfo.value = row.memo
  }
  
  onMounted(() => {
    searchButton()
  })
  </script>
  
  <style scoped lang="less">
  :deep(.el-input__wrapper) {
    width: 120px;
  }
  
  .account-table {
    .account-role {
      overflow: hidden;
      white-space: nowrap;
      text-overflow: ellipsis;
    }
  }
  
  .checked {
    .el-checkbox {
      width: 100px;
    }
  }
  
  .phone {
    display: flex;
  
    p {
      font-size: 22px;
    }
  
    .one {
      margin: 0 10px;
  
      .onee {
        width: 80px;
      }
    }
  
    .two {
      .twoo {
        width: 180px;
      }
    }
  }
  
  .secret-key {
    display: flex;
  
    .one {
      .onee {
        width: 200px;
        margin-right: 20px;
      }
    }
  }
  
  .el-button--text {
    margin-right: 15px;
  }
  
  .dialog-footer button:first-child {
    margin-right: 10px;
  }
  
  .search {
    display: flex;
    margin-bottom: 2px;
    align-items: center;
    justify-content: space-between;
  
    .search_left {
      > div {
        margin-bottom: 10px;
        margin-right: 10px;
      }
  
      .el-input {
        width: 150px;
      }
  
      .el-select {
        width: 150px;
      }
  
      .el-button {
        margin-bottom: 10px;
      }
    }
  
    .search_right {
      .el-button {
        margin-bottom: 10px;
      }
    }
  }
  
  .search_left {
    > div {
      margin-bottom: 10px;
      margin-right: 10px;
    }
  
    .el-input {
      width: 200px;
    }
  }
  
  .el-dropdown,
  .el-link__inner {
    cursor: pointer;
  }
  </style>
  No newline at end of file
  
  