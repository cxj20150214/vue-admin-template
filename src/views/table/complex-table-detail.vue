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
            <p class="p2">
              <el-tag type="success"> 定义中 </el-tag>
            </p>
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
    <div class="filter-container" v-show="this.active == 0">
      <div class="titleBox"><h3>基本信息</h3></div>
      <div class="bzBox">
        <el-form
          ref="dataForm"
          :rules="rules"
          :model="temp"
          label-position="left"
          label-width="80px"
          style="width: 400px; margin-left: 50px"
        >
          <el-form-item label="变量名称" prop="name" width="80px">
            <el-input v-model="temp.name" />
          </el-form-item>
          <el-form-item label="变量类型" prop="type">
            <el-select
              v-model="temp.type"
              class="filter-item"
              placeholder="请选择"
            >
              <el-option
                v-for="item in varType"
                :key="item"
                :label="item"
                :value="item"
              />
            </el-select>
            <p class="intro">设定变量的归属类型，便于后续分类检索。</p>
          </el-form-item>
          <el-form-item label="变量描述" prop="remark">
            <el-input
              v-model="temp.remark"
              :autosize="{ minRows: 2, maxRows: 4 }"
              type="textarea"
              placeholder="请填写描述.."
            />
          </el-form-item>
        </el-form>
      </div>
    </div>
    <div class="filter-container" v-show="this.active == 0">
      <div class="titleBox"><h3>运行信息</h3></div>
      <div class="bzBox">
        <el-form
          ref="dataForm"
          :rules="rules"
          :model="temp"
          label-position="left"
          label-width="80px"
          style="width: 450px; margin-left: 50px"
        >
          <el-form-item label="统计维度" prop="dimension">
            <el-select
              v-model="temp.dimension"
              style="width: 400px"
              class="filter-item"
              placeholder="请选择"
              multiple
            >
              <el-option
                v-for="item in dimensionList"
                :key="item.value"
                :label="item.label"
                :value="item.value"
              />
            </el-select>
            <p class="intro">
              设定变量的统计维度，作为聚合条件，比如：客户编号。
            </p>
          </el-form-item>
          <el-form-item label="统计字段" prop="field" width="80px">
            <el-input v-model="temp.field" />
            <p class="intro">
              设定统计数据字段，用于信息展示，比如：Sum(交易金额)。
            </p>
          </el-form-item>
          <el-form-item label="统计方法" prop="method">
            <el-select
              v-model="temp.method"
              class="filter-item"
              placeholder="请选择"
            >
              <el-option
                v-for="item in methodList"
                :key="item.value"
                :label="item.label"
                :value="item.value"
              />
            </el-select>
            <p class="intro">
              设定统计方法，作用于统计数据，比如：COUNT(统计数据）。
            </p>
          </el-form-item>
          <el-form-item label="统计时间" prop="timeNum" style="">
            <el-input
              type="number"
              v-model="temp.timeNum"
              style="width: 70%; float: left"
            />
            <el-select
              v-model="temp.time"
              class="filter-item"
              placeholder="请选择"
              style="width: 30%; float: left"
            >
              <el-option
                v-for="item in timeList"
                :key="item.value"
                :label="item.label"
                :value="item.value"
              />
            </el-select>
            <p class="intro">
              即统计最近一段时间的数据，单位为天、月、年，比如“当天”设定为“0天"，“当月”设定为“0月”，“最近3天”设定为3天等。
            </p>
          </el-form-item>
        </el-form>
      </div>
    </div>
    <div class="filter-container" v-show="this.active == 1">
      <div class="titleBox"><h3>规则配置器</h3></div>
      <div class="pzBox">
        <div class="boxLeft">
          <div class="ssbox">
            <el-input
              size="mini"
              placeholder="请输入"
              prefix-icon="el-icon-search"
              v-model="inputSS"
            >
            </el-input>
          </div>
          <div class="listbox">
            <p class="title">字段列表：</p>
            <ul>
              <li v-for="item in this.zdList">
                <p>{{item.name}}</p>
                <i class="el-icon-circle-plus-outline"></i>
              </li>
            </ul>
          </div>
        </div>
        <div class="boxRight">1</div>
      </div>
    </div>
    <div class="filter-container">
      <div class="buttonBox">
        <el-button
          style="margin-left: 150px; margin-top: 12px"
          @click="pre"
          :disabled="this.active == 0"
          >上一步</el-button
        >
        <el-button
          style="margin-top: 12px"
          type="primary"
          @click="next"
          :disabled="this.active == 2"
          >下一步</el-button
        >
      </div>
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
      active: 1,
      inputSS: "", //配置器搜索
      temp: {
        id: undefined,
        name: "",
        type: "",
        remark: "",
        dimension: [],
        field: "",
        method: "",
        timeNum: "",
        time: "",
      },
      varType: ["客户信息", "商户信息", "终端信息"],
      zdList: [
        {
          name: "商户名称",
        },
        {
          name: "商户状态",
        },
        {
          name: "集团商户标志",
        },
        {
          name: "法人证件号码",
        },
      ], //字段列表
      dimensionList: [
        {
          value: "选项1",
          label: "6 | 平台客户号",
        },
        {
          value: "选项2",
          label: "7 | 平台客户号",
        },
        {
          value: "选项3",
          label: "8 | 平台客户号",
        },
      ],
      methodList: [
        {
          value: "选项1",
          label: "方法1",
        },
        {
          value: "选项2",
          label: "方法2",
        },
        {
          value: "选项3",
          label: "方法3",
        },
      ],
      timeList: [
        {
          value: "选项1",
          label: "天",
        },
        {
          value: "选项2",
          label: "月",
        },
        {
          value: "选项3",
          label: "年",
        },
      ],
      rules: {
        name: [
          { required: true, message: "变量名称为必填项。", trigger: "blur" },
        ],
        type: [
          { required: true, message: "变量类型为必填项。", trigger: "blur" },
        ],
        remark: [
          { required: true, message: "变量描述为必填项。", trigger: "blur" },
        ],
        dimension: [
          { required: true, message: "统计维度为必填项。", trigger: "blur" },
        ],
        field: [
          { required: true, message: "统计字段为必填项。", trigger: "blur" },
        ],
        method: [
          { required: true, message: "统计方法为必填项。", trigger: "blur" },
        ],
        timeNum: [
          { required: true, message: "统计时间为必填项。", trigger: "blur" },
        ],
      },
    };
  },
  created() {
    this.temp.type = this.varType[0];
    this.temp.method = this.methodList[0].value;
    this.temp.dimension = [this.dimensionList[0].value];
    this.temp.time = this.timeList[0].value;
  },
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
.buttonBox {
  width: 450px;
  margin: 0px auto;
}
.intro {
  margin-bottom: 0px;
  font-size: 12px;
  line-height: 25px;
  color: #999;
}
.titleBox {
  background-color: #fff;
  padding: 0px;
}
.bzBox {
  width: 100%;
  padding: 20px 30%;
  background-color: #fff;
}
.pzBox {
  width: 100%;
  min-height: 500px;
  background-color: #fff;
  display: flex;
  flex-direction: row;
  .boxLeft {
    width: 25%;
    border-right: 1px solid #ddd;
    display: flex;
    flex-direction: column;
    .ssbox {
      width: 90%;
      margin: 10px auto;
    }
    .listbox {
      width: 95%;
      margin: 0px auto;
      // background-color: #F0F2F5;
      .title {
        font-size: 12px;
        padding: 0px 8px;
        color: #666;
        margin-top: 0px;
      }
      ul {
        margin: 0px;
        padding: 0px;
        li {
          display: flex;
          flex-direction: row;
          justify-content: space-between;
          font-size: 14px;
          border-radius: 2px;
          color: #666;
          list-style-type: none;
          padding: 10px;
          padding-left: 15px;
          margin: 5px;
          box-shadow: 1px 1px 5px rgba(0, 0, 0, 0.2);
          background-color: #fffdf4;
          &:hover {
            box-shadow: 1px 1px 5px rgba(0, 0, 0, 0.4);
          }
          i {
            cursor: pointer;
          }
          p {
            margin: 0px;
          }
        }
      }
    }
  }
  .boxRight {
    width: 75%;
  }
}
.app-container {
  padding: 0px;
}
.filter-container {
  padding: 20px;
  padding-top: 0px;
  background-color: #f0f2f5;
  display: flex;
  flex-direction: column;
  h3 {
    font-size: 14px;
    padding: 20px;
    margin: 0px;
    border-bottom: 1px solid #f2f2f2;
  }
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
          font-size: 16px;
        }
      }
    }
  }
}
</style>
