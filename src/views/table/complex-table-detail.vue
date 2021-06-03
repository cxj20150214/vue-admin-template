<template>
  <div class="app-container">
    <el-container>
      <div class="jcBox">
        <div class="box_l">
          <h2>
            <img class="logo" src="../../assets/u41.png" alt="" />商户注册资料表
            | 商户状态
          </h2>
          <p class="text">105+4位地区码+8位流水号</p>
          <div class="detail">
            <p>创建人：<span>曲丽丽</span></p>
            <p>创建时间：<span>2016-06-16 14:03</span></p>
            <p>生效日期：<span>2016-06-16 14:03</span></p>
          </div>
        </div>
        <div class="box_r">
          <div class="txt">
            <p class="p1">状态</p>
            <p class="p2"><span>定义中</span></p>
          </div>
        </div>
      </div>
    </el-container>
    <div class="filter-container top">
      <div class="bzBox">
        <el-steps :active="active" finish-status="success" align-center>
          <el-step title="变量详情"></el-step>
          <el-step title="变量配置"></el-step>
          <el-step title="变量审核"></el-step>
        </el-steps>
      </div>
    </div>
    <div class="filter-container">
      <el-button style="margin-top: 12px" @click="pre">上一步</el-button>
      <el-button style="margin-top: 12px" @click="next">下一步</el-button>
    </div>
  </div>
</template>

<script>
import waves from "@/directive/waves"; // waves directive
import { parseTime } from "@/utils";
import Pagination from "@/components/Pagination"; // secondary package based on el-pagination
export default {
  name: "ComplexTableDetail",
  components: { Pagination },
  directives: { waves },
  filters: {
    statusFilter(status) {
      const statusMap = {
        published: "success",
        draft: "info",
        deleted: "danger",
      };
      return statusMap[status];
    },
    typeFilter(type) {
      return calendarTypeKeyValue[type];
    },
  },
  data() {
    return {
      active: 0,
    };
  },
  created() {},
  methods: {
    pre() {
      if (this.active-- == 0) this.active = 3;
    },
    next() {
      if (this.active++ > 2) this.active = 0;
    },
  },
};
</script>
<style lang="scss">
.el-step__head.is-success {
  color: #009dff;
  border-color: #009dff;
}
.el-step__title.is-success {
  color: #009dff;
}
</style>
<style lang="scss" scoped>
.bzBox {
  width: 100%;
  padding: 20px 30%;
  background-color: #fff;
}
.app-container {
  padding: 0px;
}
.filter-container {
  padding: 20px;
  padding-top: 0px;
  background-color: #f0f2f5;
  &.top {
    padding-top: 15px;
  }
}
.jcBox {
  width: 100%;
  display: flex;
  flex-direction: row;
  justify-content: space-between;
  padding: 20px;
  // padding-bottom:10px;
  // border-bottom: 1px solid #eee;
  .box_l {
    width: 65%;
    display: flex;
    flex-direction: column;
    .detail {
      display: flex;
      flex-direction: row;
      font-size: 12px;
      color: #666;
      padding-left: 50px;
      span {
        color: #999;
      }
      p {
        margin-right: 80px;
      }
    }
    h2 {
      line-height: 40px;
      font-size: 18px;
      position: relative;
      margin: 0px;
      padding-left: 50px;
    }
    .text {
      font-size: 12px;
      color: #666;
      padding-left: 50px;
      line-height: 30px;
    }
    .logo {
      width: 40px;
      height: 40px;
      position: absolute;
      left: 0px;
    }
  }
  .box_r {
    width: 30%;
    display: flex;
    flex-direction: row;
    .txt {
      width: 100%;
      text-align: right;
      margin-top: 15%;
      color: #999;
      font-size: 14px;
      .p2 {
        span {
          font-size: 18px;
          color: #333;
        }
      }
    }
  }
}
</style>
