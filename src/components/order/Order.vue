<template>
  <div>
    <!-- 面包屑导航区 -->
    <el-breadcrumb separator="/">
      <el-breadcrumb-item :to="{ path: '/' }">首页</el-breadcrumb-item>
      <el-breadcrumb-item>订单管理</el-breadcrumb-item>
      <el-breadcrumb-item>订单列表</el-breadcrumb-item>
    </el-breadcrumb>

    <!-- 卡片试图区 -->
    <el-card>
      <el-row>
        <el-col :span="8">
          <el-input placeholder="请输入内容" v-model="queryInfo.query" clearable @clear="getOrderList">
            <el-button slot="append" icon="el-icon-search" @click="getOrderList"></el-button>
          </el-input>
        </el-col>    
      </el-row>   
      <!-- 订单列表数据 -->
      <el-table :data="orderlist" border stripe>
        <el-table-column type="index"></el-table-column>
        <el-table-column label="订单编号" prop="order_number"></el-table-column>
        <el-table-column label="订单价格(元)" prop="order_price"></el-table-column>
        <el-table-column label="是否付款" prop="pay_status">
            <template slot-scope="scope">
              <el-tag type="success" v-if="scope.row.pay_status==='1'">已付款</el-tag>
              <el-tag tupe="danger">未付款</el-tag>
            </template>
        </el-table-column>
        <el-table-column label="是否发货" prop="is_send">
            <template slot-scope="scope">
                <template>
                  {{scope.row.is_send}}
                </template>
            </template>
        </el-table-column>
        <el-table-column label="下单时间" prop="create_time" width="180px">
            <template slot-scope="scope">
                {{scope.row.create_time | dataFormat}}
            </template>
        </el-table-column>
        <el-table-column label="操作">
            <template>
               <el-button size="mini" type="primary" icon="el-icon-edit"
               @click="showBox"></el-button>
               <el-button size="mini" type="success" icon="el-icon-location"
               @click="showProgressBox"></el-button>    
            </template> 
        </el-table-column>
      </el-table>
    </el-card>

     <!-- 分页区域 -->
      <el-pagination
        @size-change="handleSizeChange"
        @current-change="handleCurrentChange"
        :current-page="queryInfo.pagenum"
        :page-sizes="[1, 2, 5, 10]"
        :page-size="queryInfo.pagesize"
        layout="total, sizes, prev, pager, next, jumper"
        :total="total"
      ></el-pagination>

      <!-- 修改对话框地址 -->
       <el-dialog title="修改地址" :visible.sync="addressVisible" width="50%"
       @close="addressDialogClosed">
         <el-form :model="addressForm" :rules="addressFormRules" ref="addressFormRef"
         label-width="100px">
             <el-form-item label="省市区/县" prop="address1">
               <el-input v-model="addressForm.address2"></el-input>
             </el-form-item>
             <el-form-item label="详细地址" prop="address2">
               <el-input v-model="addressForm.address2"></el-input>
             </el-form-item>
         </el-form>
         <span slot="footer" class="dialog-footer">
             <el-button @click="addressVisible=false">取消</el-button>
             <el-button @click="addressVisible=false" type="primary">确定</el-button>
         </span>
       </el-dialog>

       <!-- 查看物流信息 -->
       <el-dialog title="物流进度" :visible.sync="progressVisible" width="50%">
           <span>!</span>
       </el-dialog>
  </div>
</template>

<script>
export default {
    data() {
        return {
            queryInfo: {
                query:'',
                pagenum: 1,
                pagesize: 10
            },
            total: 0,
            orderlist: [],
            addressVisible: false,
            addressForm:{
                address1:'',
                address2:'',
            },
            addressFormRules:{
              address1:[{
                  required:true,message:'请填写省市区/县',tigger:blur
              }],
              address2:[{
                  required:true,message:'请填写具体地址',tigger:blur
              }]
            },
            progressInfo:[],
            progressVisible:false,
        }
    },
    created() {
        this.getOrderList()
    },
    methods: {
        async getOrderList() {
            const {data:res} = await this.$http.get('orders',{ params: this.queryInfo})
            
            if(res.meta.status !==200){
                return this.$message.error('获取订单列表失败!')
            }
        
        console.log(res);
        this.total=res.data.total
        this.orderlist=res.data.goods
        },
        //分页的js
        handleSizeChange(newSize){
            this.queryInfo.pagesize=newSize
            this.getOrderList()
        },
        handleCurrentChange(newPage){
            this.queryInfo.pagenum=newPage
            this.getOrderList()
        },
        //修改韩式对话框
        showBox(){
            this.addressVisible=true
        },
        addressDialogClosed(){
            this.$refs.addressFormRef.resetFields()
        },
        //查看物流信息
        async showProgressBox(){
            const {data:res} = await this.$http.get('/kuaidi/80490574412544580')
            if(res.meta.status!==200){
                return this.$meassage.error('获取物流进度失败')
            }
            this.progressInfo=res.data
            this.progressVisible=true
            console.log(this.progessInfo);
        }
    }
}
</script>

<style lang="less" scoped>

</style>