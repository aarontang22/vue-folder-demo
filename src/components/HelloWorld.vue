<template>
  <div>
    <el-row class="table-header">
      <el-col :span="6">
        <div class="grid-content">编号</div>
      </el-col>
      <el-col :span="6">
        <div class="grid-content text-left">类别名称</div>
      </el-col>
      <el-col :span="6">
        <div class="grid-content">排序</div>
      </el-col>
      <el-col :span="6">
        <div class="grid-content">操作</div>
      </el-col>
    </el-row>
    <el-row v-for="v in data" v-show="v.expand">
      <el-col :span="6">
        <div class="grid-content">{{v.id}}</div>
      </el-col>
      <el-col :span="6">
        <div v-on:click="handleNodeClick(v)" class="grid-content text-left" :style="'padding-left:'+v.level+'em'"><i
          v-bind:class="getIconClass(v)"></i>
          <i
            class="el-icon-message"></i>{{v.label}}
        </div>
      </el-col>
      <el-col :span="6">
        <div class="grid-content"><input type="number" :value="v.id"/></div>
      </el-col>
      <el-col :span="6">
        <div class="grid-content">
          <el-button type="primary">添加子类</el-button>
          <el-button type="warning">修改</el-button>
        </div>
      </el-col>
    </el-row>
  </div>
</template>

<script>
  /**
   * 设计思路:创建一个一维数组指针指向->多维数数据元素
   */
  export default {
    data () {
      let data = [{
        id: '28',
        label: '电视剧',
        children: [{
          id: '29',
          label: '菜单二级',
          children: [{
            id: '30',
            label: '菜单 三级'
          }]
        }]
      }, {
        id: '31',
        label: '电影',
        children: [{
          id: '32',
          label: '华语',
          children: [{
            id: '33',
            label: '菜单 三级'
          }]
        }, {
          id: '34',
          label: '好莱屋',
          children: [{
            id: '35',
            label: '菜单 三级'
          }]
        }]
      }]

      let linearArray = []

      /**
       * 创建一个一维数组指针指向->多维数数据元素
       * @param data
       *
       * 这里的level是为了指定是多少组菜单,这样,就可以缩进效果进行调整,level越大,缩进越大.
       * 这里的expand为了控制显示和展示,初始化时,第一级是展示的,二级以上,都是隐藏的.
       */
      function createLinearArray (data, level = 1) {
        data.forEach(v => {
          v.level = level
          if (v.level === 1) {
            v.expand = true
          } else {
            v.expand = false
          }
          linearArray.push(v)
          if (v.children) {
            createLinearArray(v.children, level + 1)
          }
        })
      }

      /**
       * 注意排序,这是为了方便按顺序的展示,这是为了按顺序展示数据
       *
       */
      linearArray.sort((a, b) => {
        return a.id - b.id
      })
      createLinearArray(data)
      return {
        data: linearArray,
        defaultProps: {
          children: 'children',
          label: 'label'
        }
      }
    },
    methods: {
      /**
       * 点击菜单时,会返回当前菜单下的子菜单数据,从这个方法获取
       * @param data
       */
      handleNodeClick (v) {
        this.renderMenu(v)
      },
      /**
       * 每次点击时,对expand显示隐藏进行操作
       * @param v
       * @param isexpand
       */
      renderMenu (v, isexpand) {
        if (v.children) {
          v.children.forEach((vv1) => {
            if (typeof isexpand === 'undefined') {
              vv1.expand = !vv1.expand

            } else {
              vv1.expand = isexpand
            }
            if (vv1.children && !vv1.expand) {
              this.renderMenu(vv1, false)
            }
          })
        }
      },
      ischildrenExpand (v) {
        return v.children.every(vv11 => vv11.expand)
      },
      getIconClass (v) {
        return (v.children && this.ischildrenExpand(v)) ? 'el-icon-minus' : (v.children && !this.ischildrenExpand(v) ? 'el-icon-plus' : 'el-icon-circle-check-outline')
      }
    }
  }
</script>

<style scoped>
  .text-left {
    text-align: left;
  }

  .el-row {
    border-bottom: #ccc solid 1px;
  }

  .grid-content {
    line-height: 36px;
  }

  .table-header {
    background: #f2f2f2;
  }
</style>
