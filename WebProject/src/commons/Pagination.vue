/**
 * 分页组件
 * 父组件更新需要调用reload方法。
 * @author xiaowenjie https://github.com/xwjie
 */
<template>
    <el-pagination
      @size-change="handleSizeChange"
      @current-change="handleCurrentChange"
      :current-page="page"
      :page-sizes="[5, 10, 50, 100]"
      :page-size="pagesize"
      layout="total, sizes, prev, pager, next, jumper"
      :total="total">
    </el-pagination>  
</template>

<script>
export default {
  props: {
    url: {
      type: String,
      required: true
    },
    value: {
      type: Array,
      required: true
    },
    sort: {
      type: Object,
      required: false,
      default: function(){
        return {};
      }
    },
    pageSize:{
      type: Number,
      required: false,
      default: 10
    },
    keyword: {
      type: String,
      required: false,
      default: ""
    }
  },
  mounted() {
    this.reload();
  },
  methods: {
    handleSizeChange(val) {
      this.pagesize = val;
      this.reload();
    },
    handleCurrentChange(val) {
      this.page = val;
      this.reload();
    },
    reload() {
      this.ajax
        .get(this.url, {
          params: {
            page: this.page,
            pagesize: this.pagesize,
            sortfield: this.sort.prop,
            sort: this.sort.order,
            keyword: this.keyword
          }
        })
        .then(result => {
          if (result.code == 0) {
            this.page = result.data.page;
            this.pagesize = result.data.pagesize;
            this.total = result.data.total;

            this.$emit("input", result.data.rows);
          } else {
            this.error(result.msg);
          }
        });
    }
  },
  data() {
    return {
      page: 1,
      pagesize: this.pageSize,
      total: 0
    };
  },
  watch: {
    sort(val) {
      this.reload();
    },
    keyword(val) {
      this.reload();
    }
  }
};
</script>


<style>

</style>
