<template>
  <div class="nav-menu">
    <div class="logo">
      <img class="img" src="~@/assets/img/logo.svg" alt="logo" />
      <span v-if="!collapse" class="title">Vue3+TS</span>
    </div>
    <el-menu :default-active="defaultActive" class="el-menu-vertical" :collapse="collapse" background-color="#0c2135" text-color="#b7bdc3" active-text-color="#0a60bd">
      <template v-for="item in userMenus" :key="item.id">
        <!-- 二级菜单 -->
        <template v-if="item.type === 1">
          <!-- 二级菜单的可以展开的标题 -->
          <el-sub-menu :index="item.id + '' ">
            <template #title>
              <el-icon v-if="item.name === '系统总览'">
                <platform />
              </el-icon>
              <el-icon v-if="item.name === '系统管理'">
                <setting />
              </el-icon>
              <el-icon v-if="item.name === '商品中心'">
                <shopping-bag />
              </el-icon>
              <el-icon v-if="item.name === '随便聊聊'">
                <chat-line-round />
              </el-icon>
              <span>{{item.name}}</span>
            </template>
            <!-- 遍历里面的item -->
            <template v-for="subitem in item.children" :key="subitem.id">
              <el-menu-item :index="subitem.id + ''" @click="handleMenuItemClick(subitem)">
                <el-icon></el-icon>
                <span>{{subitem.name}}</span>
              </el-menu-item>
            </template>
          </el-sub-menu>
        </template>
        <!-- 一级菜单 -->
        <template v-else-if="item.type === 2">
          <el-menu-item :index="item.id + ''">
            <span>{{item.name}}</span>
          </el-menu-item>
        </template>
      </template>
    </el-menu>
  </div>
</template>

<script lang="ts">
import { defineComponent, computed, ref } from 'vue'
import { useStore } from '@/store'
import { pathMapToMenu } from '@/utils/map-menus'
import {
  Platform,
  Setting,
  ShoppingBag,
  ChatLineRound
} from '@element-plus/icons-vue'
import { useRouter, useRoute } from 'vue-router'

// vuex - typescript  => pinia

export default defineComponent({
  components: {
    Platform,
    Setting,
    ShoppingBag,
    ChatLineRound
  },
  props: {
    collapse: {
      type: Boolean,
      default: false
    }
  },
  setup() {
    const store = useStore()
    const router = useRouter()
    const route = useRoute()
    const currentPath = route.path

    // 获取userMenus数据
    const userMenus = computed(() => {
      return store.state.login.userMenus
    })

    // defaultActive: 默认选中
    const menu = pathMapToMenu(userMenus.value, currentPath)
    const defaultActive = ref(menu.id + '')

    // 页面跳转
    const handleMenuItemClick = (item: any) => {
      router.push({
        path: item.url ?? '/NotFound'
      })
    }

    return {
      userMenus,
      defaultActive,
      handleMenuItemClick
    }
  }
})
</script>

<style scoped lang="less">
.nav-menu {
  height: 100%;
  background-color: #001529;

  .logo {
    display: flex;
    height: 28px;
    padding: 12px 10px 8px 10px;
    flex-direction: row;
    justify-content: flex-start;
    align-items: center;

    .img {
      height: 100%;
      margin: 0 10px;
    }

    .title {
      font-size: 16px;
      font-weight: 700;
      color: white;
    }
  }

  .el-menu {
    border-right: none;
  }

  // 目录
  .el-submenu {
    background-color: #001529 !important;
    // 二级菜单 ( 默认背景 )
    .el-menu-item {
      padding-left: 50px !important;
      background-color: #0c2135 !important;
    }
  }

  ::v-deep .el-submenu__title {
    background-color: #001529 !important;
  }

  // hover 高亮
  .el-menu-item:hover {
    color: #fff !important; // 菜单
  }

  .el-menu-item.is-active {
    color: #fff !important;
    background-color: #0a60bd !important;
  }
}

.el-menu-vertical:not(.el-menu--collapse) {
  width: 100%;
  height: calc(100% - 48px);
}
</style>