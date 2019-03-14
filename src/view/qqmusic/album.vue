<template>
  <div>
    <Card>
      <div class="search-con search-con-top">
  	      <Input placeholder="输入专辑名称搜索" class="search-input" v-model="albumName"/>
  	      <Button @click="queryPage" class="search-btn" icon="ios-search" type="primary">搜索</Button>
  	  </div>
      <div>
        <tables :loading="loading" :highlight-row="true" :no-data-text="noDataText" ref="tables" v-model="tableData" :columns="columns" @on-delete="handleDelete"/>  
      </div>
      <div style="text-align: right;margin-top: 5px;">
      	<Page :total="total" show-total show-elevator show-sizer @on-change="changePageNum" @on-page-size-change="changePageSize" size="small"/>
      </div>  
  </Card>
  </div>
</template>

<script>
import Tables from '_c/tables'
import axios from 'axios';
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
        { title: '专辑封面', key: 'albumPic', width:100,
        	render: (h, params) => {
        		return h('div', [
                    h('img', {
                        attrs: {
                            src: params.row.albumPic,
                            style: 'width: 35px;border-radius: 2px;margin-top: 5px;'
                        }
                    })
                ]);

	        } 
        },
        { title: '专辑MID', key: 'albumMid', width:150 },
        { title: '专辑名称', key: 'albumName', width: 200 },
        { title: '专辑类型', key: 'albumType',width:100 },
        { title: '专辑语言', key: 'albumLan',width:100 },
        { title: '专辑描述', key: 'albumDesc',
            render: (h, params) => {
              return h('Tooltip', {
                          props: { 
                            placement: 'left',
                          }
                      }, 
                      [
                        params.row.albumDesc,
                        h('span', { 
                            slot: 'content', 
                            style: { 
                              whiteSpace: 'normal',
                              wordBreak: 'break-all'
                            } 
                          },
                          params.row.albumDesc
                        )
                      ]
                  )
            } 
        }
      ],
      tableData: [],
      total: 0,
      pageNum: 1,
      pageSize: 10,
      albumName: ''
    }
  },
  methods: {
    handleDelete (params) {
      console.log(params)
    },
    changePageNum (pageNum) {
    	this.pageNum = pageNum;
    	this.queryPage();
    },
    changePageSize (pageSize) {
    	this.pageSize = pageSize;
    	this.queryPage();
    },
    queryPage(){
  		let that = this;
  		that.loading = true;
  		let requestUrl = '/cheetah/api/qqmusic/album/page?pageNum='+ that.pageNum+'&pageSize='+ that.pageSize;
  		if(that.albumName.trim().length > 0){
  			requestUrl += '&album_name='+encodeURIComponent(that.albumName.trim());
  		}
	    axios.get(requestUrl).then((res) => {
	    	that.tableData = res.data.data.records;
	    	that.total = res.data.data.total;
	    	that.loading = false;
	    });
    }
  },
  mounted () {
  	 this.queryPage();
  }
}
</script>

<style>

</style>
