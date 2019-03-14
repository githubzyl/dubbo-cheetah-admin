<template>
  <div>
    <Card>
      <div class="search-con search-con-top">
        <Input placeholder="输入歌手名称搜索" class="search-input" v-model="singerName"/>
        <Button @click="queryPage" class="search-btn" icon="ios-search" type="primary">搜索</Button>
      </div>
      <div>
        <tables :loading="loading"
          :highlight-row="true"
          :no-data-text="noDataText"
          ref="tables"
          v-model="tableData"
          :columns="columns"
          @on-delete="handleDelete"/>
      </div>
      <div style="text-align: right;margin-top: 5px;">
        <Page :total="total" show-total show-elevator show-sizer
          @on-change="changePageNum"
          @on-page-size-change="changePageSize" size="small"/>
      </div>
    </Card>
  </div>
</template>

<script>
import Tables from '_c/tables'
import axios from 'axios'
export default {
  name: 'singer_list',
  components: {
    Tables
  },
  data () {
    return {
      loading: true,
      noDataText: '暂无数据',
      columns: [
        {
          title: '歌手头像',
          key: 'singerPic',
          width: 100,
          render: (h, params) => {
            return h('div', [
              h('img', {
                attrs: {
                  src: params.row.singerPic,
                  style: 'width: 35px;border-radius: 2px;margin-top: 5px;'
                }
              })
            ])
          }
        },
        { title: '歌手MID', key: 'singerMid', width: 150 },
        { title: '歌手名称', key: 'singerName', editable: true },
        { title: '所属国家', key: 'country', editable: true },
        {
          title: '操作',
          key: 'handle',
          button: (h, params) => {
            return h('Button', {
              props: {
                type: 'primary'
              },
              on: {
                click: () => {
                  this.lookSingerSummary(params.row.singerMid, params.row.singerName)
                }
              }
            }, '查看个人简介'
            )
          }
        }
      ],
      tableData: [],
      total: 0,
      pageNum: 1,
      pageSize: 10,
      singerName: '',
      singerSummary: ''
    }
  },
  methods: {
    handleDelete (params) {
      console.log(params)
    },
    changePageNum (pageNum) {
      this.pageNum = pageNum
      this.queryPage()
    },
    changePageSize (pageSize) {
      this.pageSize = pageSize
      this.queryPage()
    },
    queryPage () {
      let that = this
      that.loading = true
      let requestUrl = '/cheetah/api/qqmusic/singer/page?pageNum=' + that.pageNum + '&pageSize=' + that.pageSize
      if (that.singerName.trim().length > 0) {
        requestUrl += '&singer_name=' + encodeURIComponent(that.singerName.trim())
      }
      axios.get(requestUrl).then((res) => {
        that.tableData = res.data.data.records
        that.total = res.data.data.total
        that.loading = false
      })
    },
    lookSingerSummary (singerMid, singerName) {
      let that = this
      let requestUrl = '/cheetah/reptile/qqmusic/singer/desc?singer_mid=' + singerMid
      axios.get(requestUrl).then((res) => {
        that.singerSummary = res.data.data
        that.$Modal.info({
          title: singerName + '的个人简介',
          scrollable: true,
          width: 550,
          okText: '关闭',
          closable: true,
          render: (h) => {
            return h('div', {
              style: {
                maxHeight: '350px',
                overflowX: 'hidden',
                overflowY: 'auto'
              },
              domProps: {
                innerHTML: that.singerSummary
              }
            })
          }
        })
      })
    }
  },
  mounted () {
    this.queryPage()
  }
}
</script>

<style>

</style>
