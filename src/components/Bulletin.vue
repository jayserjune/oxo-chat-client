<template>
  <div class="contactOuter">
    <div class="contact setting_wrap">
      <el-form :label-position="labelPosition" label-width="80px" size="mini">
        <h1 class="setting_title">公告</h1>
        <el-form-item label="搜索：">
          <el-input v-model="q" name="q"></el-input>
        </el-form-item>
        <dl class="dllist">
          <dd class="dl_item" id="tableWrap">
            <el-table :data="tabledata" style="width: 100%">
              <el-table-column label="账号" width="200">
                <template slot-scope="scope">
                  {{ getNameByAddress(scope.row.address) }}
                </template>
              </el-table-column>
              <el-table-column prop="sequence" label="序号" width="100">
                <template slot-scope="scope">
                  <h5 @click="loadBulletin(scope.row.hash)">{{ scope.row.sequence }}</h5>
                </template>
              </el-table-column>
              <el-table-column label="创建时间" width="200">
                <template slot-scope="scope">
                  {{scope.row.created_at | time}}
                </template>
              </el-table-column>
            </el-table>
          </dd>
        </dl>
      </el-form>
      <el-dialog ref="quote-list" title="公告详情" :visible.sync="dialogVisible" width="80%" @close='hideBulletin()'>
        <ul class="quote-dialist">
          <BulletinDialog v-for="(bulletin,index) in displayBulletins" :bulletin="bulletin" :address="address" :key="index">
          </BulletinDialog>
        </ul>
      </el-dialog>
    </div>
  </div>
</template>
<script>
import HeaderSection from './section/HeaderSection.vue'
import BulletinDialog from './BulletinDialog.vue'
import { mapActions, mapMutations, mapGetters } from 'vuex'

export default {
  name: "Bulletin",
  components: {
    HeaderSection,
    BulletinDialog
  },
  data() {
    return {
      address: this.$store.state.OXO.Address,
      labelPosition: 'top',
      q: ""
    }
  },
  created() {
    this.$store.state.OXO.SearchBulletins = []
  },
  computed: {
    ...mapGetters({
      getSearchBulletins: 'getSearchBulletins',
      getNameByAddress: 'getNameByAddress',
      displayBulletins: 'displayBulletins'
    }),
    tabledata: function() {
      return this.getSearchBulletins;
    },
    dialogVisible: function() {
      return this.displayBulletins.length;
    }
  },
  methods: {
    loadBulletin(hash) {
      this.$store.commit({
        type: 'LoadBulletin',
        hash: hash
      })
    },
    hideBulletin() {
      this.$store.commit({
        type: 'HideBulletin'
      })
    }
  },
  watch: {
    'getSearchBulletins': function() {
      this.tabledata = this.$store.state.OXO.SearchBulletins
    },
    'q': function() {
      let q = this.q.trim()
      if (q == "") {
        return
      } else {
        this.$store.commit({
          type: 'SearchBulletin',
          q: q
        })
      }
    }
  }
}

</script>
<style scoped>
.quotes-list {
  padding-bottom: 4px;
  text-align: left;
}

.quotes-list>>>.el-tag {
  margin-bottom: 5px;
  margin-right: 5px;
}

.quote-dialist {
  max-height: 400px;
  overflow-y: auto;
}

</style>
