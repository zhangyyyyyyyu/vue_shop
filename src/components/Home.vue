<template>
    <el-container class="home-container">

      <!-- 头部区域 -->
      <el-header>
        <div>
          <img src="../assets/e_commerce.png" />
          <span>电商后台管理系统</span>
        </div>
        <el-button type="info" @click="logout">退出</el-button>
      </el-header>

      <!-- 下侧内容区域 -->
      <el-container>
        
        <!-- 左边栏区域 -->
        <el-aside :width="isCollapse ? '64px' : '200px'">
            <div class="toggle-button" @click="toggleCollapse">||| </div>
          <!-- 左侧菜单区域 -->
          <el-menu background-color="#333744" text-color="#fff"
           active-text-color="#409EFF" unique-opened :collapse="isCollapse"
           :collapse-transition="true" router :default-active="activePath">
            <!-- 一级菜单 -->
            <el-submenu :index="item.id + ''" v-for="item in menulist" :key="item.id">
              <!-- 一级菜单模板 -->
              <template slot="title">
                <!-- 图标 -->
                <i :class="iconsObj[item.id]"></i>
                <!-- 文本 -->
                <span>{{item.authName}}</span>
              </template>
              <!-- 二级菜单 -->
              <el-menu-item :index=" '/' +subItem.path" v-for="subItem in item.children" 
              :key="subItem.id" @click="saveNavState(activePath)">
               <template slot="title">
                <!-- 图标 -->
                <i class="el-icon-menu"></i>
                <!-- 文本 -->
                <span>{{subItem.authName}}</span>
               </template>
              </el-menu-item>
            </el-submenu>
          </el-menu>
        </el-aside>

        <!-- 右侧主题区域 -->
        <el-main>
          <!-- 路由占位符 -->
          <router-view></router-view>
        </el-main>
        
      </el-container>
    </el-container>
</template>

<script>
export default {
    data(){
      return {
          //左侧菜单数据
          menulist: [],
          iconsObj: {
              '125': 'iconfont icon-user',
              '103': 'iconfont icon-tijikongjian',
              '101': 'iconfont icon-shangpin',
              '102': 'iconfont icon-danju',
              '145': 'iconfont icon-baobiao'
          },
          //是否折叠
          isCollapse:"false",
          //被激活的链接地址
          activePath: ''
      }
    },
      created() {
          this.getMenuList()
          //将二级菜单对应的路径通过sessionStorage取出->让此二级菜单项高亮
          this.activePath=window.sessionStorage.getItem('activePath')
    },
  methods: {
    logout() {
      //退出时首先清空储存的token
      window.sessionStorage.clear()
      //接着返回到/login页面
      this.$router.push('/login')
    },
    //获取所有菜单
    async getMenuList(){
        const { data:res }=await this.$http.get('menus')
        if(res.meta.status!==200) return this.$message.error(res.meta.msg)
        this.menulist=res.data
    },
    //点击按钮，切换菜单的折叠与展开
    toggleCollapse() {
      this.isCollapse = !this.isCollapse
    },
    //保存连接的激活状态
    saveNavState(activePath){
        window.sessionStorage.setItem('activePath',activePath)
        this.activePath=activePath
    }
  },
}
</script>

<style lang="less" scoped>
.home-container {
  height: 100%;
}
.el-header {
  background-color: #373d41;
  display: flex;
  justify-content: space-between;
  padding-left: 0;
  align-items: center;
  color: #fff;
  font-size: 20px;
  div {
    display: flex;
    align-items: center;
    span {
      margin-left: 15px;
    }
  }
}
.el-aside {
  background-color: #333744;
    .el-menu{
        border-right: none;
    }
}
.el-main {
  background-color: #eaedf1;
  float: right;
} 
.iconfont{
    margin-right: 10px;
}
.toggle-button{
    background-color: #4a5064;
    font-size: 10px;
    line-height: 24px;
    color:#fff;
    text-align:center;
    letter-spacing: 0.2em;
    cursor:pointer;
}
//存在问题
//2.一级菜单的小图标没有显示
</style>
