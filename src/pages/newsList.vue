<template>
  <div class="body">
    <Title/>
    <div class="newsList">
        <div>
         <!-- <div>
              <el-tabs v-model="activeName" @tab-click="handleClick">
                <el-tab-pane label="进行中" name="1">进行中</el-tab-pane>
                <el-tab-pane label="往期" name="0">往期</el-tab-pane>
              </el-tabs>
            </div> -->
            <ul>
                <li v-for="(item, index) in newsDataList" :key="index">
                    <div>
                        <router-link tag="a" :to="{path:'/NewsDetails',query:{id:item.id}}">{{item.title}}</router-link><br>
                        <span>{{item.createTime}}</span>
                        <p>{{item.intro}}</p>
                    </div>
                    <router-link tag="div" :to="{path:'/NewsDetails',query:{id:item.id}}">阅读详情</router-link>
                </li>

            </ul>
            <div class="block" v-if="total>0">
                <el-pagination
                background
                @size-change="handleSizeChange"
                @current-change="handleCurrentChange"
                :current-page="currentPage"
                :page-size="pageSize"
                layout="total , prev, pager, next, jumper"
                :total="total">
                </el-pagination>
            </div>
        </div>
        <div>
            <dl>
                <dt>德恒新闻</dt>
                <dd v-for="(item, index) in newsCategoryList" :key="index">
                	<a tag="a" @click="getNews(item.id)">{{item.name}}</a>
                </dd>
            </dl>
            <dl>
                <dt>相关搜索</dt>
                <dd>
                    <p>关键字：</p>
                    <input type="text" placeholder="请输入关键字"  @focus="focus" @blur="blur" :value="key">
                    <button @click="getNewsList()">搜索</button>
                </dd>
            </dl>
        </div>
    </div>
    <Bottom/>
  </div>
</template>

<script>
import api from '../utils/api.js'
import Title from '../components/Title'
import Banner from '../components/Banner'
import Bottom from '../components/Bottom'

export default {
    name: 'newsList',
    components: {
        Title,
        Banner,
        Bottom
    },
    data() {
        return {
        currentPage: 1,
        total:0,
        pageSize:5,
        newsDataList: [],
        newsCategoryList: [],
        key:"",
        catId:"",
        activeName:"1",
        };
    },
    created() {
      //获取分类
           this.getCategoryList()
           //获取列表数据
           this.getNewsList()
    },
    methods: {
        handleSizeChange(val) {
        console.log(`每页 ${val} 条`);
        this.currentPage = 1;
        this.pageSize = val;
        },
        handleCurrentChange(val) {
        console.log(`当前页: ${val}`);
        this.$data.currentPage=val
        this.getNewsList()
        },
        // 聚焦事件
        focus: function(e) {
                console.log("聚焦")
              },
        //失焦事件
        blur: function(e) {

                this.key = e.target.value
                console.log("获取到的关键词为："+this.key)
                // this.getNewsList()
              },
        //获取分类
        getCategoryList(){
        	api.getNewsCategory().then(res => {
        		console.log(res)
        		if(res.data.result.length>0)
        		{
        			this.$data.newsCategoryList=res.data.result
        				console.log("获取分类",this.$data.newsCategoryList)
        		}
        	}).catch(e => {
        		console.log("请求错误", e)
        	})
        },
        //获取选择的分类
        getNews(val) {
        this.catId=val
        console.log("当前分类：",this.catId)
        this.getNewsList()
        },

        getNewsList(){
          console.log("获取数据")
        	api.getNewsList({"catId":this.catId,"pageNo":this.currentPage,"pageSize":this.pageSize,"title":"*"+this.key+"*"}).then(res => {
        		console.log(res)
            this.$data.total=res.data.result.total
            this.$data.currentPage=res.data.result.current
        		if(res.data.success)
        		{
        			this.$data.newsDataList=res.data.result.records
        				console.log("最后数据为",this.$data.newsDataList)

        		}
        	}).catch(e => {
        		console.log("请求错误", e)
        	})
        }
    },

}
</script>

<style scoped>
.body{
    width: 100%;
    min-width: 1080px;
    background-color: #fff;
}
.newsList{
    width: 1080px;
    min-height: 500px;
    margin: auto;
    padding-top: 100px;
}
.newsList > div:nth-of-type(1){
    width: calc(100% - 320px);
    margin-left: 8px;
    float: left;
}
.newsList > div:nth-of-type(1) > div:nth-of-type(1){
    width: 100%;
    height: 40px;
    line-height: 40px;
    background: #FAFAFA;
    border: 1px solid #E6E6E6;
    margin-bottom: 10px;
    margin-left:0;
}
.newsList > div:nth-of-type(1) > div:nth-of-type(1) div,section{
    width: 100px;
    height: 100%;
    text-align: center;
    float: left;
}
.init{
    background: #D10101;
    color: #fff;
}
.newsList > div:nth-of-type(1) > div:nth-of-type(1) section{
    float: right;
}
.newsList > div:nth-of-type(1) > ul{
    width: 100%;
}
.newsList > div:nth-of-type(1) > ul > li{
    width: 100%;
    overflow: hidden;
    border-bottom: 1px solid #ccc;
    padding-bottom: 23px;
    margin: 0 55px 30px 0;
}
.newsList > div:nth-of-type(1) > ul > li > div:nth-of-type(1){
    width: calc( 100% - 100px );
    float: left;
}
.newsList > div:nth-of-type(1) > ul > li > div:nth-of-type(1) > span{
    color: #999;
    font-size: 13px;
    line-height: 13px;
}
.newsList > div:nth-of-type(1) > ul > li > div:nth-of-type(1) > a{
    color: #292929;
    font-size: 20px;
    line-height: 28px;
    text-decoration: none;
}
.newsList > div:nth-of-type(1) > ul > li > div:nth-of-type(1) > p{
    color: #666;
    width: 98%;
    max-height: 150px;
    font-size: 14px;
    line-height: 24px;
    overflow:hidden;
    /* word-break:keep-all;
    white-space:nowrap;
    text-overflow:ellipsis; */
}
.newsList > div:nth-of-type(1) > ul > li > div:nth-of-type(2){
    width: 98px;
    height: 32px;
    line-height: 32px;
    float: left;
    text-align: center;
    border: 1px solid #000;
    margin-top: 56px;
}
.newsList > div:nth-of-type(1) > ul > li > div:nth-of-type(2):hover{
    background: #D10101;
    border: 1px solid #D10101;
    color: #fff;
}

.newsList > div:nth-of-type(2){
    width: 265px;
    height: 500px;
    float: right;
    color: #555;
}
.newsList > div:nth-of-type(2) dl{
    margin-bottom: 45px;
}
.newsList > div:nth-of-type(2) dl dt{
    width: 100%;
    height: 40px;
    font-size: 16px;
    line-height: 24px;
    letter-spacing: 2px;
    font-weight: bold;
    color: #555;
    margin-bottom: 10px;
    border-bottom: 1px solid #ccc;
}
.newsList > div:nth-of-type(2) dl:nth-of-type(1) dd{
    height: 35px;
    font-size: 13px;
    line-height: 35px;
    border-bottom: 1px #eee dotted;
}
.newsList > div:nth-of-type(2) dl:nth-of-type(2) dd p{
    height: 40px;
    font-size: 13px;
    line-height: 40px;
}
.newsList > div:nth-of-type(2) dl:nth-of-type(2) dd input{
    width: 100%;
    height: 40px;
    box-sizing: border-box;
    padding: 0 5px;
}
.newsList > div:nth-of-type(2) dl:nth-of-type(2) dd button{
    width: 100%;
    height: 40px;
    margin-top: 10px;
    box-sizing: border-box;
    background: #D10101;
    color: #fff;
    border: 1px solid #ccc;
    border-radius: 3px;
    padding: 0 5px;
}
.block{
    margin: 0 0 100px 60px;
}
</style>

<style>
.el-pagination.is-background .btn-next, .el-pagination.is-background .btn-prev, .el-pagination.is-background .el-pager li{
    width: 34px;
    height: 34px;
    text-align: center;
    line-height: 34px;
    border: 1px solid #ddd;
    border-radius: 0;
}
.el-pagination button, .el-pagination span:not([class*=suffix]){
    line-height: 34px;
}
.el-pagination__editor.el-input .el-input__inner{
    width: 34px;
    height: 34px;
    text-align: center;
    line-height: 34px;
    border-radius: 0;
}
.el-pagination.is-background .el-pager li:not(.disabled).active{
  background-color: #D10101;
}
</style>
