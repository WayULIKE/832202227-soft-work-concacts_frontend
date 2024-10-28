<template>
<div>
  <div>
  <el-input style="width:250px" v-model="params.name" placeholder="请输入查找信息"></el-input>
  <el-button style="margin-left:25px;" type="warning" @click="find()">查找</el-button>
  <el-button style="margin-left:25px;" type="warning" @click="reset()">清空搜索栏</el-button>
  <el-button style="margin-left:25px;" type="success" @click="add()">添加新的联系人</el-button>
     <el-table
           :data="tableData"
           style="width: 100%">
           <el-table-column
             prop="no"
             label="编号"
             width="150">
           </el-table-column>
           <el-table-column
             prop="name"
             label="姓名"
             width="200">
           </el-table-column>
           <el-table-column style="margin-left:50px;"
             prop="phone"
             label="电话"
             width="500">
           </el-table-column>
            <el-table-column label="操作">
              <template slot-scope="scope">
                 <el-button type="primary" @click="edit(scope.row)">编辑</el-button>
                 <el-popconfirm title="确定删除吗？" @confirm="del(scope.row.id)">
                   <el-button style="margin-left:25px;" slot="reference" type="danger">删除</el-button>
                 </el-popconfirm>
              </template>
               </el-table-column>
         </el-table>
  </div>

<div style="margin-top: 10px">
  <el-pagination
      @size-change="handleSizeChange"
      @current-change="handleCurrentChange"
      :current-page="params.pageNum"
      :page-sizes="[5, 10, 15, 20]"
      :page-size="params.pageSize"
      layout="total, sizes, prev, pager, next"
      :total="total">
  </el-pagination>
</div>
<div>
  <el-dialog title="请填写信息" :visible.sync="dialogFormVisible" width="30%">
    <el-form :model="form">
    <el-form-item label="no" label-width="15%">
            <el-input v-model="form.no" autocomplete="off" style="width: 90%"></el-input>
          </el-form-item>
      <el-form-item label="姓名" label-width="15%">
        <el-input v-model="form.name" autocomplete="off" style="width: 90%"></el-input>
      </el-form-item>
      <el-form-item label="电话" label-width="15%">
        <el-input v-model="form.phone" autocomplete="off" style="width: 90%"></el-input>
      </el-form-item>
    </el-form>
    <div slot="footer" class="dialog-footer">
      <el-button @click="dialogFormVisible = false">取 消</el-button>
      <el-button type="primary" @click="submit()">确 定</el-button>
    </div>
  </el-dialog>
</div>

  </div>
</template>

 <script>

 import request from "@/utils/request";
    export default {
       name: "AboutView",
        data() {
          return {
            params:{
            name: '',
            pageNum: 1,
            pageSize: 5,
            },
            tableData: [],
            total: 0,
            dialogFormVisible:false,
            form:{}
          }
        },
        created() {
          this.find();
        },
        methods: {

          find(){
            request.get("/user/search",{
              params: this.params
            }).then(res => {
            if(res.code ==='0'){
              this.tableData = res.data.list;
              this.total = res.data.total;

            }else{

            }
          })

        },
        add(){
        this.form = {};
        this.dialogFormVisible = true;
        },
        submit(){
        request.post("/user", this.form).then(res => {
            if (res.code === '0') {
              this.$message({
                message: '操作成功',
                type: 'success'
              });
              this.dialogFormVisible = false;
              this.find();
            } else {
              this.$message({
                message: res.msg,
                type: 'success'
              });
            }
          })
        },
        edit(obj) {
        this.dialogFormVisible = true;
        this.form = obj;
        this.find();
        },
        del(id){
        request.delete("/user/" + id).then(res => {
            if (res.code === '0') {
              this.$message({
                message: '删除成功',
                type: 'success'
              });
              this.find();
            } else {
              this.$message({
                message: res.msg,
                type: 'success'
              });
            }
          })

        },

        reset(){
        this.params = {
        pageNum: 1,
        pageSize: 5,
        name:'',
        }
        this.find();
        },
        handleSizeChange(pageSize){
        this.params.pageSize = pageSize;
        this.find();
        },
        handleCurrentChange(pageNum){
        this.params.pageNum = pageNum;
        this.find();
        }
        }
    }
  </script>
