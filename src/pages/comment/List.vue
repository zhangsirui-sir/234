<template>
    <div>
        
        <!-- 按钮 -->
        <el-button type="success" size="small" @click="toAddHandler">
            添加
        </el-button>
        <el-button type="danger" size="small">
            删除
        </el-button>
       <!-- 表格 -->
        <el-table :data="comment">
            <el-table-column  fixed="left" prop="id" label="编号"></el-table-column>
            <el-table-column  fixed="left" prop="content" label="评论内容"></el-table-column>
            <el-table-column  prop="commentTime" label="评论时间"></el-table-column>
            <el-table-column  prop="orderId" label="评论用户ID"></el-table-column>
            <el-table-column fixed="right" label="操作">
               <template v-slot="slot">
                   <a href="" @click.prevent="toDeleteHandler(slot.row.id)">删除</a>
                   <a href="" @click.prevent="toUpdateHandler(slot.row)">修改</a>

               </template>
            </el-table-column>
            
        </el-table>
        <el-pagination
    layout="prev, pager, next"
    :total="50">
  </el-pagination>
  <!-- 模态框 -->
   <el-dialog
                :title="title"
                :visible.sync="visible"
                width="60%"
                >
                <el-form :model="form" label-width="80px">
                     <el-form-item label="编号">
                        <el-input  v-model="form.id">

                        </el-input>
                    </el-form-item>

                    <el-form-item label="内容">
                        <el-input v-model="form.content">

                        </el-input>
                    </el-form-item>

                
                    <el-form-item label="评论时间">
                        <el-input v-model="form.commentTime">

                        </el-input>
                      </el-form-item>  
                     <el-form-item label="评论者">
                    <el-select v-model="form.orderId" placeholder="请选择">
                        <el-option
                        v-for="item in options"
                        :key="item.value"
                        :label="item.name"
                        :value="item.parentId">
                        </el-option>
                    </el-select>
                    </el-form-item>


                </el-form>
                <span slot="footer" class="dialog-footer">
                    <el-button @click="closeModalHandler" size="small">取 消</el-button>
                    <el-button type="primary"  @click="submitHandler" size="small">确 定</el-button>
                </span>
            </el-dialog>
    </div>
</template>

<script>
import request from '@/utils/request'
import querystring from 'querystring'
export default {
    methods:{
         toAddHandler(){
             let url = "http://localhost:6677/customer/findAll"
            this.title="添加评论";
             request.get(url).then((response)=>{
                this.options = response.data;
            })
           
            this.visible=true;
            this.form={
                type:"comment"
            }
        },
        //重载员工数据
        loadData(){
            //this指向vue实例，通过vue实例访问该实例中数据
            let url="http://localhost:6677/comment/findAll"
        request.get(url).then((response)=>{
            //箭头函数中this指向外部函数中的this
            this.comment=response.data;
        })
        },
         closeModalHandler(){
            this.visible=false;
        },
         toDeleteHandler(id){
             this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
         //调用后台接口，实现删除
            let url="http://localhost:6677/comment/deleteById?id="+id;
            request.get(url).then((response)=>{
                //刷新页面
                this.loadData();
                //提示信息
                this.$message({
   
                    type: 'success',
                    message: '删除成功!'+id
                });
            })
        })
        },toUpdateHandler(row){
            let url = "http://localhost:6677/customer/findAll"
            request.get(url).then((response)=>{
                this.options = response.data;
            })
            this.title="修改评论信息";
             this.form=row;
            this.visible=true;
        },
         submitHandler(){
                //与后台进行交互
                let url="http://localhost:6677/comment/saveOrUpdate";
                
                //前端向后台发送请求，完成数据的保存操作
                request({
                    //小括号方法调用
                    url,
                    method:"Post",
                    headers:{
                        //查询字符串 type=customer&age=12
                        //通过request与后台交互
                        "Content-Type":"application/x-www-form-urlencoded"
                    },
                    data:querystring.stringify(this.form)
                }).then((response)=>{

                    this.closeModalHandler();
                    this.loadData();
                    this.$message({
                        type:"success",
                        message:response.message
                    })
                })
        },

    },
    data(){
        return{
        visible:false,
        title:"添加评论", 
        comment:[
               
            ],
             options:[],
             form:{
                type:"comment"
            }
        }
    },
    created(){
            this.loadData();
    }
    
}
</script>
<style scoped>

</style>