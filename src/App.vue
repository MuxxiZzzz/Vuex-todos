<template>
  <div id="app">
    <a-input placeholder="请输入任务" class="my_ipt" :value="inputValue" @change="handleChangeInput" />
    <a-button type="primary" @click="addItemToList">添加事项</a-button>

    <a-list bordered :dataSource="$store.getters.infoList" class="dt_list">
      <a-list-item slot="renderItem" slot-scope="item">
        <!-- 复选框 -->
        <a-checkbox
          :checked="item.done"
          @change="
            e => {
              cbStatusChanged(e, item.id)
            }
          "
        >
          {{ item.info }}
        </a-checkbox>
        <!-- 删除链接 -->
        <a slot="actions" @click="deleteItemInList(item.id)">删除</a>
      </a-list-item>

      <!-- footer区域 -->
      <div class="footer" slot="footer">
        <span>{{ unDoneLength }}条剩余</span>
        <a-button-group>
          <a-button :type="viewKey === 'all' ? 'primary' : 'default'" @click="changList('all')">全部</a-button>
          <a-button :type="viewKey === 'undone' ? 'primary' : 'default'" @click="changList('undone')">未完成</a-button>
          <a-button :type="viewKey === 'done' ? 'primary' : 'default'" @click="changList('done')">已完成</a-button>
        </a-button-group>
        <a @click="cleanItem">清除已完成</a>
      </div>
    </a-list>
  </div>
</template>
<script>
import { mapState, mapGetters } from 'vuex'
export default {
  name: 'app',
  data() {
    return {}
  },
  created() {
    this.$store.dispatch('getList')
  },
  computed: {
    // list() {
    //   return this.$store.state.list
    // },
    ...mapState(['inputValue', 'viewKey']),
    ...mapGetters(['unDoneLength']),
  },
  methods: {
    handleChangeInput(e) {
      this.$store.commit('setInputValue', e.target.value)
    },
    addItemToList() {
      //向列表中新增列表项
      if (this.inputValue.trim().length <= 0) {
        return this.$message.warning('文本框内容不能为空')
      } else {
        this.$store.commit('addItem')
      }
    },
    deleteItemInList(id) {
      this.$store.commit('deleteItem', id)
    },
    cbStatusChanged(e, id) {
      // console.log(e.target.checked) true
      // console.log(id) 正常
      const param = {
        id: id,
        e: e.target.checked,
      }
      this.$store.commit('changeDone', param)
    },
    cleanItem() {
      this.$store.commit('cleanDone')
    },
    changList(key) {
      this.$store.commit('changeViewKey', key)
    },
  },
}
</script>
<style scoped>
#app {
  padding: 10px;
}
.my_ipt {
  width: 500px;
  margin-right: 10px;
}
.dt_list {
  width: 500px;
  margin-top: 10px;
}
.footer {
  display: flex;
  justify-content: space-between;
  align-items: center;
}
</style>
