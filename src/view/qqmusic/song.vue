<template>
  <div>
    <Card>
      <div class="search-con search-con-top">
  	      <Input placeholder="输入歌曲名称搜索" class="search-input" v-model="songName"/>
  	      <Button @click="queryPage" class="search-btn" icon="ios-search" type="primary">搜索</Button>
  	  </div>
      <div>
        <audio :src="songUrl" controls autoplay></audio>当前播放：{{ currentPlaySong }}
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
  name: 'song_list',
  components: {
    Tables
  },
  data () {
    return {
      loading: true,
      noDataText: '暂无数据',
      columns: [
        { title: '歌曲MID', key: 'songMid', sortable: true },
        { title: '歌曲名称', key: 'songName' },
        { title: '所属专辑', key: 'albumName' },
        { title: '时长', key: 'duration',
                render: (h,params) => {
                    let time = params.row.duration;
                    if(null != time){
                        let m = time / 60;
                        let s = time % 60;
                        m = parseInt(m);
                        if(m < 10){
                           m = '0' + m;
                        }
                        if(s < 10){
                           s = '0' + s;
                        }
                        time = m + ':' + s;
                    }
                    return h('span', {
                        domProps: {
                          innerHTML: time
                        }
                    })
                } 
        },
        {
          title: '操作',
          key: 'handle',
          button: [(h, params, vm) => {
              let that = this;
              return h('Button', {
                  props: {
                    type: 'primary',
                  },
                  on: {
                    click: () => {
                      let requestUrl = '/cheetah/reptile/qqmusic/song/url?song_mid='+params.row.songMid;
                      axios.get(requestUrl).then((res) => {
                          that.currentPlaySong = params.row.songName;
                          that.songUrl = res.data.data;
                      });
                    }
                  },
                }, '播放')
            }
          ]
        }
      ],
      tableData: [],
      total: 0,
      pageNum: 1,
      pageSize: 10,
      songName: '',
      songUrl: '',
      currentPlaySong:''
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
  		let requestUrl = '/cheetah/api/qqmusic/song/page?pageNum='+ that.pageNum+'&pageSize='+ that.pageSize;
  		if(that.songName.trim().length > 0){
  			requestUrl += '&song_name='+encodeURIComponent(that.songName.trim());
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
