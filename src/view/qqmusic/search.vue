<template>
  <div>
    <Card>
      <div style="padding: 5px 0px;">
        <div style="margin: 0 auto;width: 495px;">
          <div style="width: 450px;float: left;">
            <AutoComplete
              v-model="keyword"
              transfer=true
              placeholder="请输入搜索关键字"
              @on-change="onChangeKeyword"
              @keyup.enter.native="onSearchByKeyword">
              <div class="mod_search" v-for="item in searchBoxData">
                  <!--单曲-->
                  <div class="search_result__sort" v-if="item.type_name == 'song'">
                    <h4 class="search_result__tit"><i :class="'search_result__icon_' + item.type_name"></i>{{ item.name }}</h4>
                    <ul class="search_result__list">
                        <Option v-for="option in item.itemlist" :value="option.name" :key="option.mid">
                          <li>
                              <a :href="'https://y.qq.com/n/yqq/song/'+ option.mid + '.html'" target="_blank" :class="'search_result__link js_smartbox_' + item.type_name" :data-docid="option.docid" :data-id="option.id" :data-mid="option.mid" :data-name="option.name">
                                  <span class="search_result__name"><span class="search_result__keyword">{{ keyword }}</span>{{ option.name.substring(keyword.length) }}</span>-
                                  <span class="search_result__singer">{{ option.singer }}</span>
                              </a>
                          </li>
                        </Option>
                    </ul>
                </div>
                <!--歌手-->
                <div class="search_result__sort" v-if="item.type_name == 'singer'">
                  <h4 class="search_result__tit"><i :class="'search_result__icon_' + item.type_name"></i>{{ item.name }}</h4>
                  <ul class="search_result__list">
                      <Option v-for="option in item.itemlist" :value="option.name" :key="option.mid">
                        <li>
                            <a :href="'https://y.qq.com/n/yqq/singer/'+ option.mid + '.html'" target="_blank" :class="'search_result__link js_smartbox_' + item.type_name" :data-docid="option.docid" :data-id="option.id" :data-mid="option.mid" :data-name="option.name">
                                <span class="search_result__name"><span class="search_result__keyword">{{ keyword }}</span>{{ option.name.substring(keyword.length) }}</span></span>
                            </a>
                        </li>
                      </Option>
                  </ul>
                </div>
                <!--专辑-->
                <div class="search_result__sort" v-if="item.type_name == 'album'">
                  <h4 class="search_result__tit"><i :class="'search_result__icon_' + item.type_name"></i>{{ item.name }}</h4>
                  <ul class="search_result__list">
                      <Option v-for="option in item.itemlist" :value="option.name" :key="option.mid">
                        <li>
                            <a :href="'https://y.qq.com/n/yqq/album/'+ option.mid + '.html'" target="_blank" :class="'search_result__link js_smartbox_' + item.type_name" :data-docid="option.docid" :data-id="option.id" :data-mid="option.mid" :data-name="option.name">
                                <span class="search_result__name"><span class="search_result__keyword">{{ keyword }}</span>{{ option.name.substring(keyword.length) }}</span>&nbsp;
                                <span class="search_result__singer">{{ option.singer }}</span>
                            </a>
                        </li>
                      </Option>
                  </ul>
                </div>
                <!--MV-->
                <div class="search_result__sort" v-if="item.type_name == 'mv'">
                  <h4 class="search_result__tit"><i :class="'search_result__icon_' + item.type_name"></i>{{ item.name }}</h4>
                  <ul class="search_result__list">
                      <Option v-for="option in item.itemlist" :value="option.name" :key="option.mid">
                        <li>
                            <a :href="'https://y.qq.com/n/yqq/mv/v/'+ option.vid + '.html'" target="_blank" :class="'search_result__link js_smartbox_' + item.type_name" :data-docid="option.docid" :data-id="option.id" :data-mid="option.mid" :data-name="option.name">
                                <span class="search_result__name"><span class="search_result__keyword">{{ keyword }}</span>{{ option.name.substring(keyword.length) }}</span>-
                                <span class="search_result__singer">{{ option.singer }}</span>
                            </a>
                        </li>
                      </Option>
                  </ul>
                </div>
              </div>
            </AutoComplete>
          </div>
          <div style="width: 45px;float: left;">
            <Button icon="ios-search" type="primary" @click="onSearchByKeyword">搜索</Button>
          </div>
          <div style="clear: both;"></div>
        </div>
      </div>
      <div style="margin-top: 20px;">
          <Tabs :animated="false" v-model="currentTab" @on-click="onChangeTab">
              <TabPane label="单曲">
                  <div>
                      <tables :loading="song.loading" :highlight-row="true" :no-data-text="noDataText" v-model="song.data" :columns="song.columns"/> 
                  </div>
                  <div style="text-align: right;margin-top: 5px;" v-if="song.page.total > 0">
                      <Page :total="song.page.total" :page-size="song.page.pageSize"  @on-change="changeSongPageNum" size="small"/>
                  </div>
              </TabPane>
              <TabPane label="专辑">
                  <div>
                      <tables :loading="album.loading" :highlight-row="true" :no-data-text="noDataText" ref="tables" v-model="album.data" :columns="album.columns"/> 
                  </div>
                  <div style="text-align: right;margin-top: 5px;" v-if="album.page.total > 0">
                      <Page :total="album.page.total" :page-size="album.page.pageSize"  @on-change="changeAlbumPageNum" size="small"/>
                  </div>
              </TabPane>
              <TabPane label="MV">
                  <div class="mod_mv_list">
                    <Spin fix v-if="mv.loading"></Spin>
                    <ul class="mv_list__list">
                      <li class="mv_list__item" v-for="item in mv.data">
                        <div class="mv_list__item_box" style="visibility: visible;">
                          <a :href="'https://y.qq.com/n/yqq/mv/v/'+ item.v_id +'.html'" class="mv_list__cover mod_cover js_mv" target="_blank">
                            <img class="mv_list__pic" :src="item.mv_pic_url" :alt="item.mv_name" style="display: block; visibility: visible;">
                            <i class="mod_cover__icon_play"></i>
                          </a>
                          <h3 class="mv_list__title"><a :href="'https://y.qq.com/n/yqq/mv/v/'+ item.v_id +'.html'" class="js_mv" target="_blank" :title="item.mv_name">{{ item.mv_name }}</a></h3>
                          <p class="mv_list__singer">
                            <a :href="'https://y.qq.com/n/yqq/singer/'+item.singerMID+'.html'" class="js_singer" target="_blank" :title="item.singer_name">{{ item.singer_name }}</a>
                          </p>
                        </div>
                      </li>
                    </ul>
                  </div>
                  <div style="text-align: right;margin-top: 5px;" v-if="mv.page.total > 0">
                      <Page :total="mv.page.total" :page-size="mv.page.pageSize"  @on-change="changeMvPageNum" size="small"/>
                  </div>
              </TabPane>
          </Tabs>
      </div>
    </Card>
  </div>
</template>

<script>
import Tables from '_c/tables'
import axios from 'axios';
export default {
  name: 'music_search',
  components: {
    Tables
  },
  data () {
    return {
       keyword: '',
       searchBoxData: [],
       noDataText: '无搜索结果',
       searchType: 1,
       currentTab: 0,
       song: {
          loading: false,
          columns: [
            { title: '歌曲', key: 'name',
                render: (h,params) => {
                    let t = params.row.title_hilight;
                    t = t.replace('<em>', '<span class="c_tx_highlight">');
                    t = t.replace('</em>', '</span>');
                    return h('a', {
                        attrs: {
                          href: 'https://y.qq.com/n/yqq/song/'+params.row.mid+'.html',
                          target: '_blank'
                        },
                        style:{
                          color:'#000'
                        },
                        domProps: {
                          innerHTML: t
                        }
                    })
                } 
            },
            { title: '歌手', key: 'singerName',
                render: (h,params) => {
                    let singers = params.row.singer;
                    let singerNames = '';
                    let split = ' / ';
                    if(null != singers && singers.length > 0){
                        for(let i = 0, length = singers.length; i < length ; i++){
                            singerNames += split + '<a href="https://y.qq.com/n/yqq/singer/'+singers[i].mid+'.html" target="_blank" style="color:#000;">'+singers[i].title_hilight+'</a>';
                            singerNames = singerNames.replace('<em>', '<span class="c_tx_highlight">');
                            singerNames = singerNames.replace('</em>', '</span>');
                        }
                        singerNames = singerNames.substring(split.length);
                    }                 
                    return h('span', {
                        domProps: {
                          innerHTML: singerNames
                        }
                    })
                } 
            },
            { title: '专辑', key: 'albumName',
                render: (h,params) => {
                    let t = params.row.album.title_hilight;
                    t = t.replace('<em>', '<span class="c_tx_highlight">');
                    t = t.replace('</em>', '</span>');
                    if('' == params.row.album.mid){
                        return h('span', {
                            domProps: {
                              innerHTML: t
                            },
                            style:{
                              color:'#000'
                            }
                        })
                    }
                    return h('a', {
                        attrs: {
                          href: 'https://y.qq.com/n/yqq/album/'+params.row.album.mid+'.html',
                          target: '_blank'
                        },
                        domProps: {
                          innerHTML: t
                        },
                        style:{
                          color:'#000'
                        }
                    })
                } 
            },
            { title: '时长', key: 'interval',
                render: (h,params) => {
                    let time = params.row.interval;
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
            }
          ],
          data: [],
          page: {
             total: 0,
             pageSize: 20,
             currPage: 1
          }
       },
       album: {
          loading: false,
          columns: [
            { title: '专辑', key: 'albumName',
                render: (h,params) => {
                    let t = params.row.albumName_hilight;
                    t = t.replace('<em>', '<span class="c_tx_highlight">');
                    t = t.replace('</em>', '</span>');
                    t = '<a href="https://y.qq.com/n/yqq/album/'+params.row.albumMID+'.html" target="_blank" style="color:#000;"><img src="'+params.row.albumPic+'" style="width:50px;float:left;"><span style="float:left;line-height:50px;margin-left: 10px;max-width: 80%;overflow: hidden;text-overflow: ellipsis;white-space: nowrap;">' + t + '</span></a>';
                    return h('div', {
                        domProps: {
                          innerHTML: t
                        },
                        style:{
                          color:'#000',
                          padding: '5px 0px 55px 0px'
                        }
                    })
                } 
            },
            { title: '歌手', key: 'songName',
                render: (h,params) => {
                    let t = params.row.singerName_hilight;
                    t = t.replace('<em>', '<span class="c_tx_highlight">');
                    t = t.replace('</em>', '</span>');
                    return h('a', {
                        attrs: {
                          href: 'https://y.qq.com/n/yqq/singer/'+params.row.singerMID+'.html',
                          target: '_blank'
                        },
                        domProps: {
                          innerHTML: t
                        },
                        style:{
                          color:'#000'
                        }
                    })
                } 
            },
            { title: '发行时间', key: 'publicTime'}
          ],
          data: [],
          page: {
             total: 0,
             pageSize: 45,
             currPage: 1
          }
      },
      mv:{
        loading: false,
        data: [],
        page: {
           total: 0,
           pageSize: 28,
           currPage: 1
        }
      }
    }
  },
  methods: {
    onChangeKeyword(){
      let that = this;
      let requestUrl = '/cheetah/reptile/qqmusic/searchbox';
      if(that.keyword.trim().length > 0){
        requestUrl += '?keyword='+encodeURIComponent(that.keyword.trim());
        axios.get(requestUrl).then((res) => {
          that.searchBoxData = res.data.data;
        });
      }
    },
    onSearchByKeyword(){
      let that = this;
      let page_num = 1;
      if(0 == that.currentTab){
          that.searchType = 1;
          that.song.loading = true;
          page_num = that.song.page.currPage;
      }else if(1 == that.currentTab){
          that.searchType = 3;
          page_num = that.album.page.currPage;
          that.album.loading = true;
      }else{
          that.searchType = 4;
          page_num = that.mv.page.currPage;
          that.mv.loading = true;
      }
      let requestUrl = '/cheetah/reptile/qqmusic/search?search_type='+ that.searchType + '&page_num=' + page_num;
      if(that.keyword.trim().length > 0){
        requestUrl += '&keyword='+encodeURIComponent(that.keyword.trim());
        axios.get(requestUrl).then((res) => {              
           if(0 == that.currentTab){
                that.song.data = res.data.data.song.list;
                that.song.page.total = res.data.data.song.totalnum;
           }else if(1 == that.currentTab){
                that.album.data = res.data.data.album.list;
                that.album.page.total = res.data.data.album.totalnum;
           }else{
                that.mv.data = res.data.data.mv.list;
                that.mv.page.total = res.data.data.mv.totalnum;
           }
           that.closeLoading(); 
        });
      }else{
        that.closeLoading();
      }
    },
    changeSongPageNum(page_num){
      this.song.page.currPage = page_num;
      this.onSearchByKeyword();
    },
    changeAlbumPageNum(page_num){
      this.album.page.currPage = page_num;
      this.onSearchByKeyword();
    },
    changeMvPageNum(page_num){
      this.mv.page.currPage = page_num;
      this.onSearchByKeyword();
    },
    onChangeTab(name){
      this.currentTab = name;
      this.onSearchByKeyword();
    },
    closeLoading(){
      let that = this;
      if(0 == that.currentTab){
          that.song.loading = false;
      }else if(1 == that.currentTab){
          that.album.loading = false;
      }else{
          that.mv.loading = false;
      }
    }
  },
  mounted () {
  	 
  }
}
</script>

<style>
.mod_search .search_result__sort {
    position: relative;
    padding: 0;
    border-top: 0;
}
.mod_search .search_result__tit i {
    left: 18px;
}
.search_result__tit i {
    position: absolute;
    left: 15px;
    width: 16px;
    height: 16px;
    overflow: hidden;
    background-image: -webkit-image-set(url(https://y.gtimg.cn/mediastyle/yqq/img/icon_sprite.png?max_age=2592000&v=07c1fc9fc5592c08947c2049926c9ea1&v=07c1fc9fc5592c08947c2049926c9ea1) 1x,url(https://y.gtimg.cn/mediastyle/yqq/img/icon_sprite@2x.png?max_age=2592000&v=07c1fc9fc5592c08947c2049926c9ea1&v=07c1fc9fc5592c08947c2049926c9ea1&v=436a840da7095677a3d5910557b04015) 2x);
    background-repeat: no-repeat;
}
.search_result__icon_song {
    top: 10px;
    background-position: -20px -220px;
}
.search_result__icon_singer {
    top: 10px;
    background-position: -20px -240px;
}
.search_result__icon_album {
    top: 10px;
    background-position: -40px 0;
}
.search_result__icon_mv {
    top: 12px;
    background-position: 0 -220px;
}
.mod_search .search_result__tit {
    width: 100px;
    padding: 0 11px 0 41px;
    line-height: 36px;
    color: #999;
}
.mod_search .search_result__list {
    border-left: 1px solid #d4d4d4;
    border-top: 1px solid #f2f2f2;
    margin-left: 92px;
    padding: 5px 0;
    margin-top: -42px;
}
.search_result__link {
    overflow: hidden;
    white-space: nowrap;
    text-overflow: ellipsis;
    padding: 0 5px 0 5px;
    line-height: 20px;
    color: #999;
}
.search_result__link span {
    cursor: pointer;
}
.search_result__keyword {
    color: #31c27c;
}
.search_result__name {
    color: #333;
}
.c_tx_highlight {
    color: #31c27c;
}
a:hover{
  color: #31c27c !important;
  text-decoration: none;
}
.mod_mv_list{
  margin-top: -20px;
}
.mv_list__item {
    float: left;
    width: 25%;
    overflow: hidden;
}
.mv_list__item_box {
    position: relative;
    margin-right: 20px;
}
.mv_list__cover {
    position: relative;
    display: block;
    overflow: hidden;
    padding-top: 56.5476%;
    margin-bottom: 15px;
}
.mv_list__pic {
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    transform: scale(1);
    transition: transform .75s;
}
.mod_cover__icon_play {
    background-image: -webkit-image-set(url(https://y.gtimg.cn/mediastyle/yqq/img/cover_play.png?max_age=2592000&v=88abebcbc1242dbbbbc836cc3e04a006) 1x,url(https://y.gtimg.cn/mediastyle/yqq/img/cover_play@2x.png?max_age=2592000&v=88abebcbc1242dbbbbc836cc3e04a006&v=10e4305a2558d496548955434eaa30d9) 2x);
    position: absolute;
    left: 50%;
    top: 50%;
    margin: -35px;
    width: 70px;
    height: 70px;
    transform: scale(.7) translateZ(0);
    transition-property: opacity,transform;
    transition-duration: .5s;
    zoom: 1;
    opacity: 0;
}
.mv_list__title {
    overflow: hidden;
    text-overflow: ellipsis;
    white-space: nowrap;
    font-weight: 400;
    zoom: 1;
}
.mv_list__singer, .mv_list__singer a {
    color: #999;
}
.mv_list__singer {
    height: 24px;
}
.mv_list__desc, .mv_list__singer, .mv_list__title {
    overflow: hidden;
    text-overflow: ellipsis;
    white-space: nowrap;
}
.mod_cover:hover .mod_cover__icon_play {
    opacity: .9;
    -webkit-transform: scale(1) translateZ(0);
    -webkit-transition-property: opacity,-webkit-transform;
    -webkit-transition-duration: .5s;
    transform: scale(1) translateZ(0);
    transition-property: opacity,transform;
    transition-duration: .5s;
    cursor: pointer;
}
</style>
