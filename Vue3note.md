**beforeRouteUpdate**



在当前路由改变且该组件被复用时触发，可以访问 this。

```javascript
export default {
  beforeRouteUpdate(to, from, next) {
    // 逻辑代码
    next();
  }
};
```

typora访问地址

https://typoraio.cn/



毎日頑張る必要があるので、ルーズにするのはだめなんだ。

怠けてはいけないんだ



**気を抜く**：掉以轻心、放松警惕。

​	•	涉及精神和注意力的疏忽。

​	•	**手を抜く**：偷懒、马虎。

​	•	涉及实际操作和工作的疏忽或降低标准。





UserList页面table clickした後、handTabsメソッドが呼び出される。それで、ユーザーのアカウントのメッセージのダイアログが開かれる。

获取用户列表，

```typescript
const remoteUserId = async (val) => {
  console.log(val)
  if (val) {
    try {
      let res = await reqUserList({ userId: val, page: 1, size: 100 })
      remoteOptions.value = res.data.list
    } catch (err) { }
  } else {
    remoteOptions.value = []
  }
}
```

接口的内容：

```ts
//获取用户列表 精准
export const reqUserList = (data: any) => {
    return http.post("/webgw/user/list", data)
}

接口文档
designed by刘san
模糊检索 曖昧検索
https://dev-torna.port888.com/#/view/N2eNvv8e
精密検索
https://dev-torna.port888.com/#/view/wXnZ4yX9
```



每条数据内容：

```json
{ "userId": "1270280198582960131", "loginId": "andy005", "nickName": "Andy005", "regTime": 1722900000000, "userLevel": 1, "userStatus": "y", "userType": "u", "isAgent": "n", "parentId": "0", "parentLoginId": "", "parentNickName": "", "isFrontUser": "n", "teamStatus": "", "regIP": "", "innerDistribution": "", "innerTags": "", "userMemo": "不要删除-不要修改-前端开发使用-andy", "upperMemo": "", "kolMemo": "", "sysMemo": "不要删除-不要修改-前端开发使用-andy", "mobileCountryCode": "81", "mobile": "07******321", "mobileShortcut": "0721", "email": "", "regChannel": "", "regFrom": "", "regPromotionCode": "", "gender": "m", "lang": "", "lastLoginTime": 3600000, "lastLoginIP": "", "isAuthentication": "n", "isMobileAuthentication": "n", "isKyc": "n", "isBot": "n", "accountType": "p", "idcCode": "", "isBackendUser": "n", "avatar": "", "kolUserId": "1270280198582960131", "directSubCnt": 0, "totalTeamCnt": 0 }
```

原来的调用table的接口本身就具有query功能，所以我们查询具体的一条信息只需要补上query参数，返回只有一个item的数组即可。



ref搭配init写法

```ts
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
const queryForm = ref(initQueryForm())

Object.assign(queryForm.value, initQueryForm())//修改queryForm里data
```

props默认值

```ts
// 父组件传参props
interface AccountInfoProps {
  accountInfo: Object
  displayHandleButton: boolean
}
const props = withDefaults(defineProps<AccountInfoProps>(), {
  displayHandleButton:true
})
```

el-form数据塞进去的方法

```ts
 <el-form
        :model="addForm"
        ref="formRef"
        :rules="rulesAddFrom"
        style="width: 900px; margin: 0 auto"
        label-width="130"
      >
//タイプスクリプトでの具体的なメソッド
const handleEdit = (row) => {
  getAllSketchList()
  getAllSysTemplateList()
  distinguish.value = true
  let { createTime, ...rest } = row
  // isEditForm.value = true
  isForm.value = true
  Object.assign(addForm.value, { ...rest })
  console.log(addForm.value)
}
```

动态展示状态根据后台返回的字符串

```vue
<el-table-column
          label="状态"
          key="templateStatus"
          prop="templateStatus"
          align="left"
        >
          <template #="{ row }">
            <el-dropdown
              trigger="click"
              size="small"
              @command="stateChange"
            >
              <span class="el-dropdown-link">
                <el-tag
                  v-if="row.templateStatus == 'y'"
                  class="mx-1"
                  type="primary"
                >
                  启用
                </el-tag>
                <el-tag
                  v-else-if="row.templateStatus == 'm'"
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
                    :command="{ templateId: row.templateId, templateStatus: 'y' }"
                  >
                    <el-tag type="primary">启用</el-tag>
                  </el-dropdown-item>
                  <el-dropdown-item
                    class="mx-1"
                    :command="{ templateId: row.templateId, templateStatus: 'm' }"
                  >
                    <el-tag type="warning">维护</el-tag>
                  </el-dropdown-item>
                  <el-dropdown-item
                    class="mx-1"
                    :command="{ templateId: row.templateId, templateStatus: 'n' }"
                  >
                    <el-tag type="info">关闭</el-tag>
                  </el-dropdown-item>
                </el-dropdown-menu>
              </template>
            </el-dropdown>
          </template>
        </el-table-column>
```





こんにちは　みんなさんと会えて本当に光栄と感じます。小学生の時、放課後したら、絶対熱血格闘伝説というゲームしていました。

茨城の姉妹都市安慶しが出身です。四歳から父上に連れられて湖で泳ぎはじめました、父はスポーツが大好きで、僕も父に影響されてこのような習慣を身に付けていました。中学校に入ったら、夏休みになると、アジアの一番の長い川の長江を泳ぎはじめました。どんな天気でも、父は毎日退勤した後、このかわを泳ぎます。僕も冬泳ぎということを一年間続けました、寒く無いと言えないのは無論ですが、自分にとっていちばんの難しい事やれば、今は冬泳ぎを続けないけど、ランニングを続けました。ハーフマラソンを2回を走り切りました。来年の東京マラソン大会を参加したい。

一人っ子ですので、口八丁の人ではないです。今の抱負はJava資格を取る　日本語を向上させる。

