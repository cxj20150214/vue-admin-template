<template>
  <div class="app-container">
    <el-container>
      <div class="jcBox">
        <div class="box_l">
          <h2>
            <img class="logo" src="../../assets/u41.png" alt="" />基础变量管理
          </h2>
          <p class="text">
            基础变量是对数据进行初步加工生成的变量，这些变量可以再加工生成衍生变量，也可以直接映射成业务字段进行规则配置。
          </p>
          <!-- <div class="detail">
            <p>创建人：<span>曲丽丽</span></p>
            <p>创建时间：<span>2016-06-16 14:03</span></p>
            <p>生效日期：<span>2016-06-16 14:03</span></p>
          </div> -->
        </div>
        <div class="box_r">
          <div class="txt">
            <p class="p1">源系统</p>
            <p class="p2"><span>25</span>个</p>
          </div>
          <div class="txt">
            <p class="p1">基础变量数</p>
            <p class="p2"><span>10,000</span>个</p>
          </div>
          <div class="txt">
            <p class="p1">衍生变量数</p>
            <p class="p2"><span>10,000</span>个</p>
          </div>
        </div>
      </div>
    </el-container>
    <div class="filter-container top">
      <el-input
        v-model="listQuery.title"
        placeholder="变量名称"
        style="width: 200px"
        class="filter-item"
        @keyup.enter.native="handleFilter"
      />
      <!-- <el-select
        v-model="listQuery.importance"
        placeholder="全部"
        clearable
        style="width: 120px"
        class="filter-item"
      >
        <el-option
          v-for="item in importanceOptions"
          :key="item"
          :label="item"
          :value="item"
        />
      </el-select> -->
      <el-button
        v-waves
        class="filter-item"
        type="primary"
        icon="el-icon-search"
        @click="handleFilter"
      >
        搜索
      </el-button>
      <el-button
        class="filter-item"
        style="margin-left: 10px"
        type="primary"
        icon="el-icon-edit"
        @click="handleCreate"
      >
        新增
      </el-button>
    </div>
    <div class="filter-container">
      <el-table
        :key="tableKey"
        v-loading="listLoading"
        :data="list"
        border
        fit
        highlight-current-row
        style="width: 100%"
      >
        <el-table-column label="ID" width="50px" align="center">
          <template slot-scope="{ row }">
            <span>{{ row.id }}</span>
          </template>
        </el-table-column>
        <el-table-column label="变量名称" width="" align="center">
          <template slot-scope="{ row }">
            <span>{{ row.name }}</span>
          </template>
        </el-table-column>
        <el-table-column label="状态" width="80px" align="center">
          <template slot-scope="{ row }">
            <el-tag :type="row.state">
              {{ row.stateName }}
            </el-tag>
          </template>
        </el-table-column>
        <el-table-column label="数据来源" width="" align="center">
          <template slot-scope="{ row }">
            <span>{{ row.sources }}</span>
          </template>
        </el-table-column>
        <el-table-column label="统计维度" width="" align="center">
          <template slot-scope="{ row }">
            <span>{{ row.dimension }}</span>
          </template>
        </el-table-column>
        <el-table-column label="更新人" width="80px" align="center">
          <template slot-scope="{ row }">
            <span>{{ row.update }}</span>
          </template>
        </el-table-column>
        <el-table-column label="更新时间" width="" align="center">
          <template slot-scope="{ row }">
            <span>{{ row.updateTime }}</span>
          </template>
        </el-table-column>
        <el-table-column label="创建时间" width="" align="center">
          <template slot-scope="{ row }">
            <span>{{ row.setTime }}</span>
          </template>
        </el-table-column>
        <el-table-column
          label="操作"
          align="center"
          width="230"
          class-name="small-padding fixed-width"
        >
          <template slot-scope="{ row }">
            <el-button type="primary" size="mini" @click="handleUpdate(row)">
              编辑
            </el-button>
            <el-button
              v-if="row.status != 'published'"
              size="mini"
              type="success"
            >
              配置
            </el-button>
            <el-button
              v-if="row.status != 'deleted'"
              size="mini"
              type="danger"
              @click="handleModifyStatus(row, 'deleted')"
            >
              删除
            </el-button>
          </template>
        </el-table-column>
      </el-table>
    </div>
    <pagination
      v-show="total > 0"
      :total="total"
      :page.sync="listQuery.page"
      :limit.sync="listQuery.limit"
      @pagination="getList"
    />
  </div>
</template>

<script>
import {
  fetchList,
  fetchPv,
  createArticle,
  updateArticle,
} from "@/api/article";
import waves from "@/directive/waves"; // waves directive
import { parseTime } from "@/utils";
import Pagination from "@/components/Pagination"; // secondary package based on el-pagination
export default {
  name: "ComplexTable",
  components: { Pagination },
  directives: { waves },
  data() {
    return {
      tableKey: 0,
      list: null,
      total: 0,
      listLoading: true,
      listQuery: {
        page: 1,
        limit: 20,
        importance: undefined,
        title: undefined,
        type: undefined,
        sort: "+id",
      },
      // importanceOptions: ["全部", "一级", "二级", "三级"],
      temp: {
        id: undefined,
        importance: 1,
        remark: "",
        timestamp: new Date(),
        title: "",
        type: "",
        status: "published",
      },
      dialogFormVisible: false,
      dialogStatus: "",
      dialogPvVisible: false,
      pvData: [],
      downloadLoading: false,
    };
  },
  created() {
    this.getList();
  },
  methods: {
    getList() {
      this.listLoading = false;
      this.list = [
        {
          id: 1,
          name: "客户类型",
          stateName: "定义中",
          state:"success",
          sources: "商户注册资料表",
          dimension: "平台客户编号",
          update: "陈象江",
          updateTime: "2016-06-16  14:03",
          setTime: "2016-06-16  14:03",
        },
        {
          id: 2,
          name: "设备线路状况类型",
          stateName: "定义中",
          state:"success",
          sources: "商户注册资料表",
          dimension: "平台客户编号",
          update: "陈象江",
          updateTime: "2016-06-16  14:03",
          setTime: "2016-06-16  14:03",
        },
        {
          id: 4,
          name: "商户状态",
          stateName: "定义中",
          state:"success",
          sources: "商户注册资料表",
          dimension: "平台客户编号",
          update: "陈象江",
          updateTime: "2016-06-16  14:03",
          setTime: "2016-06-16  14:03",
        },
      ];
      this.total = 3;
      // fetchList(this.listQuery).then(response => {
      //   this.list = response.data.items
      //   this.total = response.data.total

      //   // Just to simulate the time of the request
      //   setTimeout(() => {
      //     this.listLoading = false
      //   }, 1.5 * 1000)
      // })
    },
    handleFilter() {
      this.listQuery.page = 1;
      this.getList();
    },
    handleModifyStatus(row, status) {
      this.$message({
        message: "操作成功",
        type: "success",
      });
      row.status = status;
    },
    resetTemp() {
      this.temp = {
        id: undefined,
        importance: 1,
        remark: "",
        timestamp: new Date(),
        title: "",
        status: "published",
        type: "",
      };
    },
    handleCreate() {
      this.resetTemp();
      this.dialogStatus = "create";
      this.dialogFormVisible = true;
      this.$nextTick(() => {
        this.$refs["dataForm"].clearValidate();
      });
    },
    handleUpdate(row) {
      // this.temp = Object.assign({}, row); // copy obj
      this.$router.push({ name: "ComplexTableDetail", query: { id: row.id } });
    },
    handleDelete(row) {
      this.$notify({
        title: "成功",
        message: "删除成功",
        type: "success",
        duration: 2000,
      });
      const index = this.list.indexOf(row);
      this.list.splice(index, 1);
    },
  },
};
</script>
<style lang="scss" scoped>
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
      width: 33.33%;
      text-align: center;
      margin-top: 4%;
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
