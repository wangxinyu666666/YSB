<template>
    <div class="details">
      <div style="margin-left: 100px;margin-top:20px">
        <el-input v-model="find" prop="find" style="width:200px" placeholder="请输入关键字"></el-input>
        <el-button icon="search" style="margin-left: 20px" type="primary" @click="doFilter()"> 搜索</el-button>
        <p>可以按昵称、真实姓名、推荐人、身份证号码手机号、状态进行搜索</p>
      </div>
      <el-table
          :data="tableDataEnd.slice((currentPage-1)*pageSize,currentPage*pageSize)"
          border
          style="margin-top: 30px; margin-left: 50px; width: 95%; text-align:center "
        >
          <el-table-column
            prop="nickname"
            label="昵称"
            width="100"
          >
          </el-table-column>
          <el-table-column
              prop="truename"
              label="真实姓名"
              width="90"
            >
          </el-table-column>
          <el-table-column
            prop="level"
            label="会员级别"
            width="100"
            >
          </el-table-column>
          <el-table-column
            prop="much"
            label="报单金额"
            width="100"
            >
          </el-table-column>
          <el-table-column
            prop="desc"
            label="身份证号码"
            width="180"
            >
          </el-table-column>
          <el-table-column
            prop="tel"
            label="手机号码"
            width="150"
            >
          </el-table-column>
          <el-table-column
            prop="refer"
            label="推荐人"
            width="100"
            >
          </el-table-column>
          <el-table-column
            prop="referpassa"
            label="推荐人密码"
            width="80"
            >
          </el-table-column>
          <el-table-column
            prop="referpassb"
            label="推荐人二级密码"
            width="80"
            >
          </el-table-column>
          <el-table-column
            prop="remark"
            label="备注"
            width="100"
            >
          </el-table-column>
          <el-table-column
            prop="time"
            label="时间"
            width="150"
            >
          </el-table-column>
          <el-table-column
            prop="state"
            label="状态"
            width="100"
            >
          </el-table-column>
            <el-table-column label="操作">
                   <template slot-scope="scope">
                      <el-button :disabled="scope.row.state == '已注册'" @click.native.prevent="test(scope.$index,tableDataEnd)" type="text" size="small"> 注册 </el-button>
                      <i class="el-icon-delete" style="margin-left: 10px;" type="button" @click="del(scope.$index,tableDataEnd)"></i>
                   </template>
            </el-table-column>
      </el-table>
      <el-pagination
        @size-change="handleSizeChange"
        @current-change="handleCurrentChange"
        :current-page="currentPage"
        :page-sizes="[1, 2, 3, 4,5,6,7,8,9,10]"
        :page-size="pageSize"
        layout="total, sizes, prev, pager, next, jumper"
        :total="tableDataEnd.length"
        style="margin-top:20px;margin-left:20px">
      </el-pagination>
    </div>
</template>

<script>
import Axios from 'axios'
export default{
  data(){
    return{
      find:'',
      tableData: [
                ],
      currentPage: 1,
      pageSize: 10,
      totalItems: 0,
      tableDataEnd:[],
      filterTableDataEnd:[],
      flag:false
    }
  },
  methods:{
    doFilter(){
      if (this.find == "") {
      this.$alert('查询条件不能为空！', '提示', {
              confirmButtonText: '确定',
            })
      return;
      }
      this.tableDataEnd = []
      //每次手动将数据置空,因为会出现多次点击搜索情况
      this.filterTableDataEnd=[]
      this.tableData.forEach((value, index) => {
        if(value.truename){
          if(value.truename.indexOf(this.find)>=0){
            this.filterTableDataEnd.push(value)
          }
        }
        if(value.nickname){
          if(value.nickname.indexOf(this.find)>=0){
            this.filterTableDataEnd.push(value)
          }
        }
        if(value.refer){
          if(value.refer.indexOf(this.find)>=0){
            this.filterTableDataEnd.push(value)
          }
        }
        if(value.desc){
          if(value.desc.indexOf(this.find)>=0){
            this.filterTableDataEnd.push(value)
          }
        }
        if(value.tel){
          if(value.tel.indexOf(this.find)>=0){
            this.filterTableDataEnd.push(value)
          }
        }
        if(value.state){
          if(value.state.indexOf(this.find)>=0){
            this.filterTableDataEnd.push(value)
          }
        }
      });
      //页面数据改变重新统计数据数量和当前页
      this.currentPage=1
      this.totalItems=this.filterTableDataEnd.length
      console.log('一共查询出'+this.totalItems);
      //渲染表格,根据值
      this.tableDataEnd=this.filterTableDataEnd
      //this.currentChangePage(this.filterTableDataEnd)
      //页面初始化数据需要判断是否检索过
      this.flag=true
    },
    handleSizeChange(val) {
      console.log(`每页 ${val} 条`);
      this.pageSize = val;
    },
    handleCurrentChange(val) {
      console.log(`当前页: ${val}`);
      this.currentPage = val;
    }, //组件自带监控当前页码
    currentChangePage(list) {
      let from = (this.currentPage - 1) * this.pageSize;
      let to = this.currentPage * this.pageSize;
      this.tableDataEnd = [];
      for (; from < to; from++) {
        if (list[from]) {
          this.tableDataEnd.push(list[from]);
        }
      }
    },

    del(index, rows){
      if(!this.flag){

      }
      index=(this.currentPage-1)*this.pageSize+index;                 
      var key=rows[index].nickname;
      console.log(index);
      this.$confirm('确定要删除'+key+'吗？', '提示', {
      //type: 'warning'
      }).then(() => {
      var data={"nickname":key};
      Axios.post('/api/del',data)
          //Axios.get('./mock/new.json')
      .then(function(res){
      if(res.data.result=="success")
      {
        this.$alert('删除成功', '提示', {
              confirmButtonText: '确定',
            })
      }
      else{
        this.$alert('删除失败', '提示', {
              confirmButtonText: '确定',
            })
      }
      }.bind(this)).catch(function(error){
        console.log("出现错误"+error.name+error.message);
      })
      }).catch(() => {
      });
    },
    test(index, rows){
      //alert(index);
      index=(this.currentPage-1)*this.pageSize+index;                 
      this.$confirm('确定已为'+rows[index].nickname+'注册成功吗？', '提示', {
        //type: 'warning'
        }).then(() => {
        var key={"nickname":rows[index].nickname};
        console.log(key);
        Axios.post('/api/register',key)
       .then(function(res){
        if(res.data.result=="success")
        {
          this.$alert('提交成功', '提示', {
                confirmButtonText: '确定',
              })
        }
        else{
          this.$alert('失败！', '提示', {
                confirmButtonText: '确定',
              })
        }
         //this.imgs=tem;
       }.bind(this))
       .catch(function(){
         console.log("error");
       })
      }).catch(() => {
        });
    },
  },
  mounted(){
    Axios.get('/api/register')
    .then(function(res){
      //成功获取json数据信息的话
      var tem=[];
      var len=res.data.length;
      for (var i = 0; i < len; i++)
      {
        tem.push({
                  nickname:res.data[i].nickname,
                  truename:res.data[i].truename,
                  refer:res.data[i].refer,
                  referpassa:res.data[i].referpassa,
                  referpassb:res.data[i].referpassb,
                  level:res.data[i].level,
                  much:res.data[i].much,
                  desc:res.data[i].desc,
                  tel:res.data[i].tel,
                  remark:res.data[i].remark,
                  time:res.data[i].time,
                  state:res.data[i].state
        })
      }
      console.log(tem);
      this.tableData=tem;
      this.tableDataEnd=this.tableData;
    }.bind(this)
    ).catch(function(error){
      console.log("出现错误"+error.name+error.massage);
    })
  }
}
</script>

<style>
body{
  margin:0;
  padding: 0;
  background:#CCCCFF;
  height: 100%;
}
p{
  font-family:"微软雅黑"; 
  font-size:14px; 
  padding-left: 5px;
  color:#666666;
}
</style>