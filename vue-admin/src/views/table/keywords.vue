<template>
  <div class="modal-vue">
    <button @click="modalShow">Add</button>
    <div v-if="open" class="overlay" @click="modelClose"></div>
    <div v-if="open" class="modal">
      <button class="close" @click="open = false">x</button>
      <h3>Key Words Add</h3>
      <el-form ref="form" :model="addKeyWords" label-width="120px">
        <el-form-item label="Category">
          <el-select v-model="addKeyWords.cate_id" placeholder="please select category">
            <el-option v-for="(cat, idx) in arrayCates" :key="idx" :label="cat.name" :value="cat.value"  />
          </el-select>
        </el-form-item>
        <el-form-item label="Words">
          <el-input v-model="addKeyWords.list_keyword" type="textarea" />
        </el-form-item>
        <el-form-item>
          <el-button type="primary" @click="addCate">Create</el-button>
        </el-form-item>
      </el-form>
    </div>
    <div class="app-container">
      <el-table
        v-loading="listLoading"
        :data="keywords"
        element-loading-text="Loading"
        border
        fit
        highlight-current-row
      >
        <el-table-column align="center" label="ID" width="95">
          <template slot-scope="scope">
            {{ scope.$index }}
          </template>
        </el-table-column>
        <el-table-column label="Key Word" align="center">
          <template slot-scope="scope">
            <span>{{ scope.row.keyword }}</span>
          </template>
        </el-table-column>
        <el-table-column label="Category" width="150" align="center">
          <template slot-scope="scope">
            <span>{{ scope.row.category }}</span>
          </template>
        </el-table-column>
        <el-table-column label="Setting" width="110" align="center">
          <template>
            <span>Setting</span>
          </template>
        </el-table-column>
      </el-table>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      keywords: [],
      addKeyWords: {
        list_keyword: [],
        cate_id: ''
      },
      listLoading: false,
      arrayCates: [],
      open: false,
      object: {
        name: 'Object Name'
      }
    }
  },
  components: {
  },
  methods: {
    addCate() {
      const requestOptions = {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify({
          'keywords': this.addKeyWords.list_keyword,
          'cate_id': this.addKeyWords.cate_id
        })
      }
      fetch('http://ssh.lalasoft.vn:8001/keywords', requestOptions).then(async response => {
        this.$router.go(this.$router.currentRoute)
      })
    },
    getKeyWorlds() {
      this.listLoading = true
      fetch('http://ssh.lalasoft.vn:8001/keywords').then(async response => {
        const datas = await response.json()
        this.keywords = datas
        this.listLoading = false
      })
    },
    getCategory(id) {
      fetch('http://ssh.lalasoft.vn:8001/categories?type_id=3').then(async response => {
        const datas = await response.json()
        for (var i = 0; i < datas.length; i += 1) {
          this.arrayCates.push({ name: datas[i].name, value: datas[i].id })
          console.log(datas[i].id)
        }
      })
    },
    modalShow() {
      this.getCategory()
      this.open = true
    },
    modelClose() {
      this.open = false
      this.arrayCates = []
    },
    methodToRunOnSelect(payload) {
      this.addKeyWords.cate_id = payload.value
    }
  },
  created() {
    this.getKeyWorlds()
  }
}
</script>
