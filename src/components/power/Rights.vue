<template>
  <div>
    <!-- 面包屑导航区 -->
    <el-breadcrumb separator-class="el-icon-arrow-right">
      <el-breadcrumb-item :to="{ path: '/welcome' }">首页</el-breadcrumb-item>
      <el-breadcrumb-item>权限管理</el-breadcrumb-item>
      <el-breadcrumb-item>权限列表</el-breadcrumb-item>
    </el-breadcrumb>

    <!-- 卡片试图区 -->
    <el-card>
      <!-- 权限列表标题 -->
      <el-table :data="rightsList" border stripe>
        <el-table-column type="index"></el-table-column>
        <el-table-column prop="authName" label="权限名称" ></el-table-column>
        <el-table-column prop="path" label="路径"></el-table-column>
        <el-table-column prop="level" label="权限等级">
          <!-- 权限内容区 -->
          <template slot-scope="scope">
            <el-tag v-if="scope.row.level === '0'">一级</el-tag>
            <el-tag type="success" v-else-if="scope.row.level === '1'">二级</el-tag>
            <el-tag type="warning" v-else-if="scope.row.level === '2'">三级</el-tag>
          </template>
        </el-table-column>
      </el-table>
    </el-card>
  </div>
</template>

<script>
export default {
    data(){
        return{
            //列表权限对象
            rightsList: []
        }
    },
    created(){
        this.getRightsList()
    },
    methods:{
        async getRightsList(){
            const {data:res}=await this.$http.get('rights/list') 
            if(res.meta.status !== 200){
                return this.$message.error('获取权限列表失败')
            }
            this.rightsList=res.data
            console.log(res);
        }
    }
}
</script>

<style lang="less" scoped>

</style>