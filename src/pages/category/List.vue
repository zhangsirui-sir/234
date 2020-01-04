<template>
    <div>
        <el-button size="small" type="success" @click="toAddHandler">添加</el-button>
        <el-button size="small" type="danger">批量删除</el-button>

        <el-table :data="programas">
            <el-table-column label="编号" fixed="left" prop="id"></el-table-column>
            <el-table-column label="栏目名称" fixed="left" prop="name"></el-table-column>
            <el-table-column label="序号" prop="num"></el-table-column>
            <el-table-column label="父栏目" prop="parentId"></el-table-column>
            <el-table-column label="操作" fixed="right">
                <template v-slot="slot">
                    <a href="" @click.prevent="toDeleteHandler(slot.row.id)">删除</a>
                    <a href="" @click.prevent="toUpdateHandler(slot.row)">修改</a>
                </template>
            </el-table-column>
        </el-table>
         <!-- 表格结束 -->
        <!-- 分页 -->
        <el-pagination
            layout="prev, pager, next"
            :total="50">
         </el-pagination>
         <!-- 分页结束 -->
         <!-- 模态框 -->
          <el-dialog
        :title="title"
        :visible.sync="visible"
        width="50%"
        >
        --{{form}}
        <el-form label-width="80px">
            <el-form-item label="栏目名称">
                <el-input v-model="form.name"/>
            </el-form-item>
            <el-form-item label="序号">
                <el-input v-model="form.num"/>
            </el-form-item>
        </el-form>
         <span slot="footer" class="dialog-footer">
            <el-button  size="small" @click="closeModalHandler">取 消</el-button>
            <el-button type="primary" @click="submitHandler" size="small">确 定</el-button>
        </span>
          </el-dialog>
    </div>    
</template>

<script>
import request from '@/utils/request'
import querystring from 'querystring'
export default {
    data(){
        return{
        visible:false,
        title:"录入信息",
        programas:[],
        form:{
                type:"category"
            }
        }
    },
     created(){
        //在页面加载出来的时候加载数据
        this.loadData();
    },
    methods:{
        toAddHandler(){
            this.form={
                type:"category"
            }
            this.visible=true;
            this.title="添加栏目信息";
        },
        loadData(){
        let url="http://localhost:6677/category/findAll"
        request.get(url).then((response)=>{//箭头函数中的this指向外部函数中的this,response是完成时要执行的函数（必写）
        this.programas = response.data;
        })
        },
        submitHandler(){
            let url = "http://localhost:6677/category/saveOrUpdate"
            //前端向后台发送请求，完成数据的保存操作
            request({
                url,
                method:"POST",
                headers:{
                    "Content-Type":"application/x-www-form-urlencoded"//告诉后台我是查询字符串
                },
                data:querystring.stringify(this.form)//将对象(this.form)转为查询字符串并发送给后台
            }).then((response)=>{
             //模态框关闭
                this.closeModalHandler();//方法中可以调用方法，记得加this
                //刷新数据
                this.loadData();
                //提示消息
                this.$message({
                    type:"success",
                    message:response.message
                    }) 
            })
        },
         closeModalHandler(){
            this.visible=false;
        },
         toDeleteHandler(id){
            //先确认
        this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
            //调用后台接口完成删除操作
            let url="http://localhost:6677/category/deleteById?id="+id
            request.get(url).then((response)=>{
                //刷新数据
                this.loadData();
                //提示结果
                this.$message({
                type: 'success',
                message:response.message
            });
            })
        })
        },
        toUpdateHandler(row){
            this.form=row;
            this.title="更新栏目信息";
            this.visible=true;
        }
    }
}
</script>

<style scoped>

</style>