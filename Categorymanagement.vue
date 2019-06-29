<template>
  <div class="mark">
    <pc-header></pc-header>
    <!--导航部分开始-->
    <pc-nav></pc-nav>
    <!--导航部分结束-->
    <main>
      <div class="mark-left">
        <li>
          <router-link :to="{name:'Commodity'}"><span>商品管理</span></router-link>
        </li>
        <li class="active">
          <router-link :to="{name:'Categorymanagement'}"><span>类目管理</span></router-link>
        </li>
        <li>
          <router-link :to="{name:'Merchants'}"><span>商家管理</span></router-link>
        </li>
        <li>
          <router-link :to="{name:'Order'}"><span>订单管理</span></router-link>
        </li>
        <li>
          <router-link :to="{name:'Platform'}"><span>平台信用管理</span></router-link>
        </li>
        <li>
          <router-link :to="{name:'Comprehensive'}"><span>综合评分管理</span></router-link>
        </li>
        <li>
          <router-link :to="{name:'ShopOrdering'}"><span>店铺排序</span></router-link>
        </li>
        <!-- <li>
          <router-link :to="{name:'PlatformScore'}"><span>平台评价</span></router-link>
      </li>  -->
      </div>

      <div class="mark-right">
        <div class="back" v-show="store_id">
          <router-link :to="{name:'Categorymanagement'}">
            <span @click="reset"> ＜ 返回</span>
          </router-link>
        </div>
        <ul class="mark-title">
          <li><span>名字</span><input type="text" class="mark-text" v-model="shop_number"></li>
          <!--<li><span>商家名称</span><input type="text" class="mark-text"></li>-->
          <li><input type="submit" id="inquires" @click="inquire" value="查询"></li>
          <li>
            <router-link :to="{'name':store_id?'Categorymanagement5':'Categorymanagement4'}"><input type="submit" id="add" value="+"></router-link>
          </li>

        </ul>
        <!-- <router-link :to="{name:'Categorymanagement4'}"><input type="submit" id="add"  value="新建" > </router-link> -->
        <table>
          <tr>
            <th>名字</th>
            <th>级别</th>
            <th>图片</th>
            <th>时间</th>
            <th>操作</th>
          </tr>
          <tr v-for="(store,index) in stores" :key="index">
            <!-- <td>
                <router-link :to="{name:'Categorymanagement2',query:{store_number:text.store_number}}">
                    <span>{{text.store_number}}
                </span></router-link>
            </td> -->
            <td><span>{{store.category_name}}</span></td>
            <td><span>{{store.category_level}}</span></td>
            <td><span class="img"><img :src="'../../../' + store.category_img" alt=""></span></td>
            <td><span class="time">{{store.create_time}}{{store.id}}</span></td>

            <td>
              <router-link v-show="!store_id" :to="{name:'Categorymanagement',query:{superior_category_id:store.id}}"><span @click="details(index)">查看</span>
              </router-link>
              <span  v-show="!store_id">/</span>
              <span class="del" @click="del(index)">删除</span>
            </td>
          </tr>
        </table>


        <div class="page">
          <el-pagination
            @size-change="handleSizeChange"
            @current-change="handleCurrentChange"
            :current-page.sync="currentPage3"
            :page-size="10"
            layout="total, prev, pager, next"
            :total="Number(total_count)">
          </el-pagination>
        </div>
      </div>
    </main>
    <pc-footer></pc-footer>
    <!--底部部分结束-->
  </div>
</template>


<script>
  import commonURL from '../../assets/js/ajax'
  import Footer from '../lib/Footer'
  import Header from '../lib/Header'
  import Nav from '../lib/Nav'

  export default {
    name: 'categorymanagement',
    data () {
      return {
        value6: '',
        stores: [
          
        ],

        currentPage3: 1,
        items: [{id: 1, text: '全部'}],
        checkboxModel: [],
        checked: false,
        textIds: [],
        shop_number: '',
        shop_name: '',
        store_number: '',
        store_name: '',
        total_count: '',
        plat_list: [],
        User_Appraise: '',
        platformAppraise: '',
        comprehensiveAppraise: '',
        store_id: ''
      }
    },
    methods: {
      handleSizeChange (val) {
        // console.log(`每页 ${val} 条`);
      },
      handleCurrentChange (val) {
        // console.log(`当前页: ${val}`);
        this.currentPage3 = val;
        var that = this;
        $.ajax({
          type: 'get',
          url: commonURL('/store/findCommodityCategory'),
          data: {
            page_index: val,
            page_count: this.total_page,
            category_id: '1'
            // home_flag: '1',
            // category_id: '1', //向后台传ID获取一二级类目
            // superior_category_id: '',
            // category_name: ''
          },
          jsonp: 'callback',
          jsonpCallback: 'success_Member',
          success: res => {
            console.log(res)
            if (res.code == 1) {
              this.total_count = res.data.pageVo.total_count
              var list = res.data.pageList
              console.log(list)
              // for (var i = 0; i < list.length; i++) {
                // that.stores.category_name = res.list[i].category_name;
                // that.stores.category_level = list[i].category_level;
                // that.stores.category_img = list[i].category_img;
                // that.stores.create_time = list[i].create_time;
              //   that.stores.push(list[i])
              // }
              that.stores = list;
            } else {
              alert(res.message)
            }
          }
        })
      },

      //查询
      inquire () {
        var that = this;
        console.log(that.shop_number)
        $.ajax({
          type: 'get',
          url: commonURL('/store/findCommodityCategory'),
          data: {
            // category_id:'1',
            category_name: that.shop_number,
            // home_flag: '1',
            // superior_category_id: '',
            // page_index: 1
          },
          jsonp: 'callback',
          jsonpCallback: 'success_Member',
          success: res => {
            if (res.code == 1) {
              this.total_count = res.data.pageVo.total_count
              var list = res.data.pageList
              // that.stores = list;
              console.log(list)
              // list.forEach(val => {
              //   val.category_img = "47.105.144.221/" + val.category_img
              // });
              that.stores = list;
              this.store_id = 1;
              // for (var i = 0; i < list.length; i++){
              // that.category_name = list[i].category_name;
              // that.category_level = list[i].category_level;
              // that.category_img = list[i].category_img;
              // that.create_time = list[i].create_time;
              // }
            } else {
              alert(res.message)
            }
          }
        })
      },

      //删除
      del (index) {
        confirm('确认要删除吗？数据不可挽回')
        var that = this
        console.log(that.stores[index].id)
        $.ajax({
          type: 'get',
          url: commonURL('/store/deleteCommodityCategory'),
          data: {
            id: that.stores[index].id  //把数据的id给到删除接口的id
            // console.log(stores.id)
          },
          success: res => {
            if (res.code == 1) {
              $.ajax({
                type: 'get',
                url: commonURL('/store/deleteCommodityCategory'),
                data: {},
                jsonp: 'callback',
                jsonpCallback: 'success_Member',
                success: res => {
                  if (res.code == 1) {
                    this.total_count = res.data.total_count
                    var list = res.data.pageList
                    console.log(list)
                    // for (var i = 0; i < list.length; i++) {
                      // that.stores.category_name = res.list[i].category_name;
                      // that.stores.category_level = list[i].category_level;
                      // that.stores.category_img = list[i].category_img;
                      // that.stores.create_time = list[i].create_time;
                    //   that.stores.push(list[i])
                    // }
                  } else {
                    alert(res.message)
                  }
                }
              })
              window.location.reload()
            } else {
              alert(res.message)
            }
          }
        })
      },

      //查看
      details(index){
        var that = this;
        $.ajax({
          type: 'get',
          url: commonURL('/store/findCommodityCategory'),
          data: {
            category_id: '2',
            superior_category_id: that.stores[index].id,
            // home_flag: '1',
            // superior_category_id: '',
            // category_name: '',
            // page_index: 1
          },
          jsonp: 'callback',
          jsonpCallback: 'success_Member',
          success: res => {
            console.log(res)
            if (res.code == 1) {
              this.total_count = res.data.pageVo.total_count
              console.log(this.total_count)
              var list = res.data.pageList
              // that.stores = list;
              // list.forEach(val => {
              //   val.category_img = "47.105.144.221/" + val.category_img
              // });
              this.store_id = that.stores[index].id;
              that.stores = list;

              console.log(this.store_id)
              // for (var i = 0; i < list.length; i++){
              // that.category_name = list[i].category_name;
              // that.category_level = list[i].category_level;
              // that.category_img = list[i].category_img;
              // that.create_time = list[i].create_time;
              // }
            } else {
              alert(res.message)
            }
          }
        })
      },

      //重置
      reset(){
        var that = this
        $.ajax({
          type: 'get',
          url: commonURL('/store/findCommodityCategory'),
          data: {
            home_flag: '1',
            category_id: '1',
            superior_category_id: '',
            category_name: '',
            page_index: 1
          },
          jsonp: 'callback',
          jsonpCallback: 'success_Member',
          success: res => {
            console.log(res.data)
            if (res.code == 1) {
              this.total_count = res.data.pageVo.total_count
              var list = res.data.pageList
              // that.stores = list;
              console.log(list)
              // list.forEach(val => {
              //   val.category_img = "47.105.144.221/" + val.category_img
              // });
              that.stores = list;
              that.store_id = '';
              // for (var i = 0; i < list.length; i++){
              // that.category_name = list[i].category_name;
              // that.category_level = list[i].category_level;
              // that.category_img = list[i].category_img;
              // that.create_time = list[i].create_time;
              // }
            } else {
              alert(res.message)
            }
          }
        })
      }
    },


    mounted () {
      var that = this
      $.ajax({
        type: 'get',
        url: commonURL('/store/findCommodityCategory'),
        data: {
          home_flag: '1',
          category_id: '1',
          superior_category_id: '',
          category_name: '',
          page_index: 1
        },
        jsonp: 'callback',
        jsonpCallback: 'success_Member',
        success: res => {
          console.log(res.data)
          if (res.code == 1) {
            this.total_count = res.data.pageVo.total_count
            console.log(this.total_count)
            var list = res.data.pageList
            // that.stores = list;
            console.log(list)
            // list.forEach(val => {
            //   val.category_img = "47.105.144.221/" + val.category_img
            // });
            that.stores = list;
            // for (var i = 0; i < list.length; i++){
            // that.category_name = list[i].category_name;
            // that.category_level = list[i].category_level;
            // that.category_img = list[i].category_img;
            // that.create_time = list[i].create_time;
            // }
          } else {
            alert(res.message)
          }
        }
      })
    },

    components: {
      'pc-footer': Footer,
      'pc-header': Header,
      'pc-nav': Nav
    }
  }
</script>


<style scoped>
  .back {
    display: block;
    /* height: 0.5rem;
    line-height: .5rem; */

  }

  .back a {
    display: block;
    font-size: .14rem;
    text-decoration: none;
    color: #333333;
    position: absolute;
    width: 60px;
    height: 30px;
    line-height: 30px;
    /* background: #8fc31f; */
    top: .1rem;
    left: .1rem;
    border-radius: 5px;
  }

  .back a span {
    color: #8fc31f;
    font-size: 14px;
  }

  .clear {
    clear: both;
  }

  .mark {
    width: 100%;
    height: 100%;
    background-color: #edecec;
  }

  main {
    margin-top: 2%;
    display: flex;
    justify-content: flex-start;
  }

  .mark-left {
    width: 12%;
    height: 7.2rem;
    background-color: #ffffff;
    margin-left: 4%;
  }

  .mark-left li {
    font-size: 16px;
    line-height: 4.38;
    text-align: center;
  }

  .mark-left li a {
    color: #8fc31f;
    text-decoration: none;
  }

  .mark-left li.active {
    font-size: 16px;
    font-weight: bold;
    text-align: center;
    color: #fff;
    position: relative;
    background-color: #8fc31f
  }

  .mark-left li.active span {
    color: #fff;
  }

  .mark-left li.active:after {
    content: "";
    display: block;
    width: 6px;
    height: 6px;
    background-color: #8fc31f;
    border-radius: 50%;
    float: left;
    position: absolute;
    top: 30px;
    left: 33px;
  }

  .mark-right {
    width: 75%;
    height: auto;
    background-color: #ffffff;
    margin-left: 2%;
    padding: 0.18rem;
    position: relative;
  }

  th {
    font-size: 14px;
  }

  .mark-right .mark-title li {
    float: left;
    font-size: 16px;
    font-weight: bold;
    line-height: 4.38;
    text-align: center;
    color: #333333;
  }

  .mark-right .mark-title li .mark-text {
    width: 1.2rem;
    height: 0.25rem;
    background-color: #ffffff;
    /* box-shadow: inset 0px 1px 3px 0 rgba(0, 0, 0, 0.2); */
    border: 1px solid #d8dce5;
    margin-left: 0.22rem;
    margin-right: 0.78rem;
    border-radius: 4px;
  }

  .mark-right .mark-title li #add {
    width: 59px;
    height: 25px;
    background-color: #8fc31f;
    text-align: center;
    color: #ffffff;
    font-size: 18px;
    border: none;
    margin-left: 20px;
    cursor: pointer;
  }

  .mark-right .mark-title li select {
    width: 120px;
    height: 25px;
    background-color: #ffffff;
    border: solid 1px #8f8f8f;
    text-align: center;
    margin-left: 15px;
  }

  .mark-right .mark-title li:nth-child(1) {
    margin-left: 6.1%;
  }

  /* #add {
    width: 100px;
    height: 25px;
    background-color: #8fc31f;
    text-align: center;
    color: #ffffff;
    font-size: 16px;
    padding: 20px;
    border-radius: 5px;
    line-height: 0px;
    border: none;
    cursor: pointer;
    float: left;
  } */
  table {
    width: 100%;
    height: auto;
    font-size: 14px;
    color: #666666;
    border-collapse: collapse;
    padding-bottom: 0.15rem;
    margin-bottom: 0.15rem;
    margin-top: 0.8rem;
  }

  table > tr:nth-child(1) {
    font-size: 16px;
    height: 0.58rem;
    margin-top: 0.3rem;
    background-color: #8fc31f;
    color: #fff;
  }

  table > tr th,
  td {
    padding: 0 0.3rem;
    text-align: center;
  }

  table > tr:not(:first-child) {
    height: 0.5rem;
    margin-top: 0.1rem;
    padding-bottom: 0.1rem;
  }

  table > tr.active {
    background-color: #ebeaea;
  }

  table > tr td {
    margin-left: 0.31rem;
    border-bottom: 1px solid #e2e2e2;
  }

  table > tr td span {
    font-size: 12px;
  }

  table > tr:not(:first-child) td:nth-child(4) {
    width: 1.6rem;
  }

  .page {
    width: 100%;
    height: 40px;
    line-height: 40px;
  }

  .del, .inquire {
    cursor: pointer;
  }

  .img > img {
    height: 55px;
  }

  #inquires {
    width: 0.69rem;
    height: 0.35rem;
    background-color: #8fc31f;
    font-size: 0.14rem;
    /* text-align: center; */
    color: #ffffff;
    border: none;
    /* float: left; */
    /* margin-left: 0.95rem; */
    border-radius: 4px;
  }
</style>
