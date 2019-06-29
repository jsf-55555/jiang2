<template>
<div class="group">
  <el-button size="mini" type="primary" @click="dialogFormVisible = true">添加会员组</el-button>
  <el-dialog title="添加会员组信息" :visible.sync="dialogFormVisible" :close-on-click-modal="false" :close-on-press-escape="true">
    <el-form :model="groupForm" :rules="rules" ref="groupForm">
      <el-form-item label="会员组名称" prop="name" :label-width="formLabelWidth">
        <el-input v-model="groupForm.name" autocomplete="off"></el-input>
      </el-form-item>
      <el-form-item label="会员组说明" prop="info" :label-width="formLabelWidth">
        <el-input v-model="groupForm.info" autocomplete="off"></el-input>
      </el-form-item>
      <el-form-item label="会员组权限" prop="level" :label-width="formLabelWidth">
        <el-checkbox-group v-model="groupForm.level">
          <el-checkbox label='1' name="level">系统设置</el-checkbox>
          <el-checkbox label='2' name="level">文章管理</el-checkbox>
          <el-checkbox label='3' name="level">用户管理</el-checkbox>
          <el-checkbox label='4' name="level">其它</el-checkbox>
        </el-checkbox-group>
      </el-form-item>
    </el-form>
    <div slot="footer" class="dialog-footer">
      <el-button type="danger" @click="dialogFormVisible=false">关 闭</el-button>
      <el-button type="info" @click="resetForm('groupForm')">重 置</el-button>
      <el-button type="primary" @click="submitForm('groupForm')">添加</el-button>
    </div>
  </el-dialog>

  <h1>会员组列表</h1>
  <el-table ref="multipleTable" :data="tableData" size="mini" style="width: 90%" >
    <el-table-column prop="uid" label="ID" min-width="40" align="center" ></el-table-column>
    <el-table-column prop="name" label="会员组名称" min-width="150"></el-table-column>
    <el-table-column prop="info" label="说明" min-width="300"></el-table-column>
    <el-table-column prop="level" label="权限列表"  :formatter="formatLevel"  width="300"></el-table-column>
    <el-table-column fixed="right" label="操作" width="250">
      <template slot-scope="scope">
          <el-button-group>
            <el-button  @click="handleClick(scope.row,scope.$index)" type="warning" size="mini" icon="el-icon-edit">编辑</el-button>
            <el-button type="danger" size="mini" icon="el-icon-delete"  @click="cancelopen(scope.$index)">删除</el-button>
          </el-button-group>
</template>
      </el-table-column>
    </el-table>
  </div>
</template>

<script>
export default {
  name: "group",
  data() {
    return {
      groupForm: {
        name: '',
        info: '',
        level: [],
      },
      formLabelWidth: '120px',
      tableData: [],
      dialogFormVisible: false, //对话框 true显示 false隐藏
      rules: {
        name: [{
            required: true,
            message: '请输入活动名称',
            trigger: 'blur'
          },
          {
            min: 1,
            max: 8,
            message: '长度在 1 到 8 个字符',
            trigger: 'blur'
          }
        ],
        level: [{
          type: 'array',
          required: true,
          message: '请至少选择一个权限',
          trigger: 'change'
        }],
        info: [{
          required: true,
          message: '请填写会员组说明',
          trigger: 'blur'
        }]
      }
    };
  },
  methods: {
    handleClick(row, id) {
      console.log(row, id);
    },
    cancelopen(id) {
        this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
          this.tableData.splice(id,1)
          this.$message({
            type: 'success',
            message: '删除成功!'
          });
        }).catch(() => {
          this.$message({
            type: 'info',
            message: '已取消删除'
          });          
        });
      },
    submitForm(formName) {
      this.$refs[formName].validate((valid) => {
        if (valid) {

          let userGroupData = {
            id: this.tableData.length+1,
            name: this.groupForm.name,
            info: this.groupForm.info,
            level: this.groupForm.level,
            send:"GroupAdd"
          }
          this.tableData.push(userGroupData)
          userGroupData = this.$stringify(userGroupData);
          this.axios.post('/static/api/userGroupDataSave.php', userGroupData)
          .then((response) => {
             let res = response.data
          })
          .catch((error)=>{
            console.log(error);
          });

          console.log(userGroupData)

          this.dialogFormVisible = false
        } else {
          console.log('error submit!!');
          return false;
        }
      });
    },
    /**
     * [formatLevel 重置权限显示效果]
     * @param  {[type]} row [description]
     * @return {[type]}     [description]
     */
    formatLevel: function(row) {
        let level = row.level;
        let str;
        level = new Array(level);
        return level
    },
    resetForm(formName) {
      this.$refs[formName].resetFields();
    }
  },
  created() {
      /**
       * [send 提交关键字，根据接口返回信息得到所有数据]
       * @type {[send]}
       */
      this.axios.get('/static/api/userGroupDataShow.php?send=GroupList')
      .then((response) => {
         let res = response.data
         this.tableData = res.msg;
         console.log(this.tableData)
      })
      .catch((error)=>{
        console.log(error);
      });
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
/*.el-dialog{
  position: relative;
}
.el-form {
  width: 95%;
}
.el-form>.el-form-item:last-child{
   display: flex;
   justify-content: flex-end;
    border-top:1px solid #ddd; 
   position: absolute;
   bottom:0;
   padding-top:.9375rem;
   left:0;
   right:0;
   padding-right:7%;
}
.dialog-footer{
  height:1.5rem;
}*/
</style>