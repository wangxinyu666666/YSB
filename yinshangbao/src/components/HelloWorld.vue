<template>
  <div class="right">
    <div class="header" style="height:50px;background:#11334D;">
      <p>基本信息</p>
    </div>
    <mt-field label="真实姓名" prop="truename" v-model="ruleForm.truename">
    </mt-field>
    <mt-field label="会员级别" prop="level" v-model="ruleForm.level" @click.native="open">
    </mt-field>
    <mt-popup v-model="ruleForm.popupVisible" position="bottom" >
      <mt-picker :slots="level" @change="getSlotValue">
      </mt-picker>
    </mt-popup>      
    <mt-field label="报单金额" prop="much" :disabled="true" v-model="ruleForm.much">
    </mt-field>
    <mt-field label="推荐人账号" prop="refer" v-model="ruleForm.refer">
    </mt-field>
    <mt-field label="推荐人密码" prop="referpassa" v-model="ruleForm.referpassa">
    </mt-field>
    <mt-field label="推荐人二级密码" prop="referpassb" v-model="ruleForm.referpassb">
    </mt-field>
    <div class="header" style="height:50px;background:#11334D;">
      <p>联系方式</p>
    </div>
    <mt-field label="身份证号码" prop="desc" v-model="ruleForm.desc">
    </mt-field>
    <mt-field label="手机号码" prop="tel" v-model="ruleForm.tel">
    </mt-field>
    <mt-field label="备注" prop="remark" v-model="ruleForm.remark">
    </mt-field>
    <p>注意：</p>
    <p>1.请仔细核查所有内容，确认无误后提交，一经注册无法修改。</p>    
    <p>2.注意选择会员星级，星级对应报单金额，如:一星700，二星2100等。</p>
    <P>3.如无推荐人则可不填推荐人账号。</P>
    <p>4.接点人默认为公司推荐排列(可能不为推荐人)，如指定接点人请备注。</p>
    <p>5.如需对冲，请备注所要对冲的账户及密码，并留下微信，对冲成功后工作人员会在两个工作日内转账。</p>
    <p>6. 工作时间：</p>
    <p>8:30-17:30报单可在一小时后登陆</p>
    <p>17:30-21:30报单可在24点前登陆</p>
    <p>21:30-次日8:30报单可在24小时内登陆</p>
    <mt-button type="primary" size="large" style="margin-top: 20px" @click.native="submitForm('ruleForm')">提交</mt-button>      
  </div>
</template>
<script>
import Axios from 'axios'
  export default {
    data () {
      return {  
        ruleForm: { 
          popupVisible:false,        
          level:'',
          refer:'',
          referpassa:'',
          referpassb:'',
          much:'',          
          desc: '',
          truename: '',
          tel:'',
          remark:''
        },
        level:[{values:['一星','二星','三星','四星','五星','六星','七星','八星','九星',]
                }],
      }
    },
    methods: {
      
      open(){
        this.ruleForm.popupVisible=true;
      },
      formReset()
      {
        document.getElementById("frm1").reset();
      },
      getSlotValue(picker){
        var _this = this;
        console.log(picker.values[0]);
        switch(picker.values[0]){
          case "一星":
            this.ruleForm.level="一星";
            this.ruleForm.much='700';
            break;
          case "二星":
            this.ruleForm.level="二星";
            this.ruleForm.much='2100';
            break;
          case "三星":
            this.ruleForm.level="三星";
            this.ruleForm.much='3500';
            break;
          case "四星": 
            this.ruleForm.level="四星";        
            this.ruleForm.much='7000';
            break;
          case "五星":
            this.ruleForm.level="五星";
            this.ruleForm.much='21000';
            break;
          case "六星":
            this.ruleForm.level="六星";
            this.ruleForm.much='35000';
            break;
          case "七星":
            this.ruleForm.level="七星";
            this.ruleForm.much='70000';
            break;
          case "八星":
            this.ruleForm.level="八星";
            this.ruleForm.much='210000';
            break;
          case "九星":
            this.ruleForm.level="九星";
            this.ruleForm.much='350000';
            break;
        }
        
      },
      NowTime() {
        var time=new Date();
        var year=time.getFullYear();//获取年
        var month=time.getMonth()+1;//或者月
        var day=time.getDate();//或者天
        var hour=time.getHours();//获取小时
        var minu =time.getMinutes();//获取分钟
        var second=time.getSeconds();//或者秒
        var data=year+"-";
        if(month<10){
            data+="0";
        }
        data+=month+"-";
        if(day<10){
            data+="0"
        }
        data+=day+" ";
        if(hour<10){
            data+="0"
        }
        data+=hour+":";
        if(minu<10){
            data+="0"
        }
        data+=minu+":";
        if(second<10){
            data+="0"
        }
        data+=second;
        return data;
      },
      submitForm(formName){
        var _this = this;
        var time=this.$options.methods.NowTime();
         
          var mydata={"truename":this.ruleForm.truename,"refer":this.ruleForm.refer,"referpassa":this.ruleForm.referpassa,"referpassb":this.ruleForm.referpassb,"level":this.ruleForm.level,"much":this.ruleForm.much,"desc":this.ruleForm.desc,"tel":this.ruleForm.tel,"remark":this.ruleForm.remark,"time":time};
          console.log(mydata);
          if(mydata.truename==""||mydata.level==""||mydata.much==""||mydata.desc==""||mydata.tel=="")
          {
             this.$messagebox.confirm('您有内容尚未填写，请填写完整再提交！')
                  .then(action => {
                    this.cancel = false;
                  })
                  .catch(action => {
                    this.cancel = true;
                  });
          }
          else{
          Axios.post('/api/Message',mydata)
          //Axios.get('./mock/new.json')
                   .then(function(res){
                    if(res.data.result=="success")
                        {
                          this.$messagebox.confirm('提交成功，您的昵称为'+res.data.nickname+'，请根据注意中的第五条的登录时间进行登录。')
                              .then(action => {
                                this.cancel = false;
                              })
                              .catch(action => {
                                this.cancel = true;
                          });
                          
                        }
                    else{
                      this.$messagebox.confirm('提交失败，请重新注册')
                              .then(action => {
                                this.cancel = false;
                              })
                              .catch(action => {
                                this.cancel = true;
                          });
                    }
                   }.bind(this)).catch(function(error){
                      console.log("出现错误"+error.name+error.message);
                    })
          }
      }
    }
  }
</script>  
<style>
body{
  margin:0;
  padding: 0;
  height: 100%;
}
.left{
  float:left;
  width: 60%;
  min-height: 450px;
}
.right{
  
}
img {
 width: 100%;
 height: 218px;
 }
 .mint-swipe {
  width:100%;
  height: 218px;
 }
.header p{
  font-family:"微软雅黑"; 
  font-size:18px; 
  padding-top:15px;
  padding-left: 10px;
  color:#FFFFFF;
}
p{
  font-family:"微软雅黑"; 
  font-size:14px; 
  padding-left: 5px;
  color:#FF0000;
}
</style>