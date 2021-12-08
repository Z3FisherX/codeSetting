<template>
	<aside class="aside">
		<el-menu
			unique-opened
			:default-active="activeMenu"
			:default-openeds="defaultOpen"
			class="el-menu-vertical-demo"
			:collapse="isCollapse"
			router
		>
			<template v-for="item in routers">
				<el-submenu
					v-if="item.children && !item.meta.hideChildren"
					:key="item.path"
					:index="item.path"
					class="submenu-list"
				>
					<template slot="title">
						<i :class="item.meta.icon"></i>
						<span>{{ $t(item.meta.title) }}</span>
					</template>
					<template v-for="subItem in item.children">
						<el-menu-item
							v-if="!subItem.meta.notMenu"
							:key="subItem.path"
							:index="subItem.path"
						>
							<div class="menu-item-container">
								<span slot="title">{{ $t(subItem.meta.title) }}</span>
							</div>
						</el-menu-item>
					</template>
				</el-submenu>
				<el-menu-item
					v-else-if="!item.meta.notMenu"
					:key="item.path"
					:index="item.path"
				>
					<div class="menu-item-container">
						<i :class="item.meta.icon"></i>
						<span slot="title">{{ $t(item.meta.title) }}</span>
					</div>
				</el-menu-item>
			</template>
		</el-menu>
	</aside>
</template>

<script>
import { mapGetters } from 'vuex';
import { routerMap } from '@/router/routerMap';

export default {
  props: {
    isCollapse: {
      type: Boolean,
      require: false,
      default: false,
    },
  },
  data: () => ({}),
  computed: {
    ...mapGetters(['menuGroup']),
    currentRoute() {
      const current = this.routers.find((router) => {
        return this.$route.matched.find((_) => {
          return router.name === _.name;
        });
      });
      return current;
    },
    activeMenu() {
      const { meta, path } = this.$route;
      // if set path, the sidebar will highlight the path you set
      if (meta.activeMenu) {
        return meta.activeMenu;
      }
      return path;
    },
    defaultOpen() {
      const result = [this.currentRoute?.path];
      return result;
    },
    routers() {
      const children = routerMap[0].children;
      const routerList = children.filter(
        (item) => item.meta.group === this.menuGroup
      );
      const handledRouters = this.modifyRouters(routerList);
      return handledRouters;
    },
  },
  methods: {
    modifyRouters(routers) {
      const a = (l) => {
        l.forEach((item) => {
          switch (item?.meta?.title) {
            case 'sidebar.order_list':
              item.meta.title = this.systemConfig?.purchaseOrder.title || '';
              item.meta.title = this.systemConfig?.purchaseOrder?.title || '';
              break;
            case 'main.special_order':
              item.meta.title = this.systemConfig?.specialOrder.title || '';
              item.meta.title = this.systemConfig?.specialOrder?.title || '';
              break;
            default:
              break;
          }
          if (item.children) a(item.children);
          return item;
        });
      };
      a(routers);
      return routers;
    },
  },
};
</script>

<style lang="less">
.aside {
	height: 100%;
	overflow-y: auto;

	.el-menu {
		height: 100%;
		.submenu-list {
			.el-menu--inline {
				.el-menu-item:last-child {
					margin-bottom: 0;
				}
				.menu-item-container {
					padding-left: 15px;
				}
			}
		}
		.iconfont {
			vertical-align: middle;
			margin-right: 8px;
			width: 24px;
			text-align: center;
			font-size: 17px;
		}
		.el-submenu__title {
			height: 36px;
			line-height: 36px;
			padding-left: 27px !important;
			font-size: 17px;
		}
		.el-menu-item {
			margin: 19px 0;
			height: 34px;
			line-height: 34px;
			.menu-item-container {
				padding-left: 7px;
				border-radius: 17px;
				span {
					font-size: 17px;
				}
			}
			&.is-active {
				background-color: #fff;
				i {
					color: #fff;
				}
				span {
					color: #fff;
				}
				.menu-item-container {
					background-color: var(--main-color, #3dcd58);
				}
			}
			&:hover {
				outline: none;
				background-color: #fff;
				i {
					transition: all 0.5s;
					color: #fff;
				}
				span {
					transition: all 0.3s;
					color: #fff;
				}
				.menu-item-container {
					transition: all 0.5s;
					background-color: var(--main-color, #3dcd58);
				}
			}
		}
	}
}
</style>
