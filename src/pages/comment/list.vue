<template>
    <div>地址管理
     <el-button type="success" @click="toAddHandler">添加</el-button>
    <el-button type="danger" >删除</el-button>
    <el-table :data="comments">
            <el-table-column  prop="id" label="编号"></el-table-column>
            <el-table-column prop="content" label="评论内容"></el-table-column>
            <el-table-column prop="commentTime" label="评论时间"></el-table-column>
            <el-table-column label="操作">
                <template v-slot="slot">
                    <a href="" @click.prevent="toDeleteHandler(slot.row.id)">删除</a>
                    <a href="" @click.prevent="toUpdateHandler(slot.row)">修改</a>
                </template>
            </el-table-column>

        </el-table>
        <el-pagination layout="prev, pager, next" :total="50"></el-pagination>
        <el-dialog title="撰写评论" :visible.sync="visible" width="60%" >
         <span>
           
             <el-form :model="from" label-width=80px>
                <el-form-item label="评论内容">
                    <el-input v-model="form.content"></el-input>
                </el-form-item>
                <el-form-item label="评论时间">
                    <el-input  v-model="form.commentTime"></el-input>
                </el-form-item>
                
             </el-form>
             </span>
         <span slot="footer" class="dialog-footer">
         <el-button @click="visible = false">取 消</el-button>
         <!-- @click=onclick -->
         <el-button type="primary" @click="submitHandler">确 定</el-button>
         </span>
        </el-dialog>
    </div>
</template>

<script>
import request from '@/utils/request'
import querystring from 'querystring'
//暴露接口
export default {
    // 存放网页中需要调用的方法
  methods:{
        loadData(){
      let url="http://localhost:6677/comment/findAll"
      request.get(url).then((response)=>{
        // 将查询结果设置到customers中，this指向外部函数的this
        this.comments = response.data;
      })
    },
    submitHandler(){
  
      let url = "http://localhost:6677/comment/saveOrUpdate";
      request({
        url,
        method:"POST",
        headers:{
          "Content-Type":"application/x-www-form-urlencoded"
        },
        data:querystring.stringify(this.form)
      }).then((response)=>{
        // 模态框关闭
        this.closeModalHandler();
        // 刷新
        this.loadData();
        // 提示消息
        this.$message({
          type:"success",
          message:response.message
        })
      })

    },
    toDeleteHandler(id){
      this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(() => {
        let url="http://localhost:6677/comment/deleteById?id="+id;
        // let that=this;
        request.get(url).then((response)=>{
            this.loadData();
        })
        this.$message({
        type: 'success',
        message: response.message
        });
      })
      
    },
    toUpdateHandler(row){
      this.visible = true;
      this.form=row;
    },
    closeModalHandler(){
      this.visible = false;
    },
    toAddHandler(){
      this.visible = true;
      this.form={
        type:"address"
      }

    }

  },
  // 用于存放要向网页中显示的数据
    //用于存放要在网页中存放的数据
    data(){
        return {
            form:{
                type:"address"
            },
            visible:false,
            comments:[]
        }

    },
    created(){
        let url="http://localhost:6677/comment/findAll"
        // let that = this
        request.get(url).then((response)=>{
            //将查询结果设置到customers中
            this.comments = response.data;
        })
        this.loadData()
        
    }

}
</script>
<style scoped>
       
</style>
