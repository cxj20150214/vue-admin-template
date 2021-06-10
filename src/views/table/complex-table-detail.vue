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
          <el-form-item label="表名称" prop="type">
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
    <!-- 配置 -->
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
              <li
                v-for="(item, index) in this.zdList"
                :key="index"
                :class="{ active: isActive === index }"
                @click="getZd(item, index)"
              >
                <p>{{ item.name }}</p>
                <div>
                  <i class="el-icon-circle-plus-outline"></i>
                  <i class="el-icon-circle-check" style="color: #fff"></i>
                </div>
              </li>
            </ul>
          </div>
        </div>
        <div class="boxRight">
          <el-tree
            :data="treeData"
            show-checkbox
            node-key="id"
            default-expand-all
            :expand-on-click-node="false"
          >
            <div class="custom-tree-node" slot-scope="{ node, data }">
              <el-select
                v-if="node.data.addType == 1"
                size="mini"
                v-model="data.treeSelect"
                placeholder="请选择"
                class="treeSelect"
                @change="seleChange(data)"
              >
                <el-option
                  v-for="item in treeList"
                  :key="item.value"
                  :label="item.label"
                  :value="item.value"
                >
                </el-option>
              </el-select>
              <div class="zdBox" v-if="node.data.addType == 0">
                <div class="title">{{ node.data.name }}</div>
                <div class="filed">
                  <el-input
                    size="medium"
                    style="width: 150px; margin-top: 5px; margin-right: 5px"
                    v-model="node.data.name"
                    :disabled="true"
                  >
                  </el-input>
                  <el-select
                    size="medium"
                    style="width: 120px; margin-top: 5px; margin-right: 5px"
                    v-model="node.data.filed_1"
                    placeholder="请选择"
                  >
                    <el-option
                      v-for="item in filed_1"
                      :key="item.value"
                      :label="item.label"
                      :value="item.value"
                    >
                    </el-option>
                  </el-select>
                  <el-select
                    size="medium"
                    style="width: 120px; margin-top: 5px; margin-right: 5px"
                    v-model="node.data.filed_2"
                    placeholder="请选择"
                  >
                    <el-option
                      v-for="item in filed_2"
                      :key="item.value"
                      :label="item.label"
                      :value="item.value"
                    >
                    </el-option>
                  </el-select>
                  <el-input
                    size="medium"
                    style="width: 160px; margin-top: 5px; margin-right: 5px"
                    v-model="node.data.text"
                    placeholder="请输入内容"
                  ></el-input>
                </div>
              </div>
              <!-- <span class="treeText">{{ node.label }}</span> -->
              <span
                style="margin-top: auto; margin-bottom: auto; margin-left: 10px"
              >
                <!-- <el-button
                  type="text"
                  @click="() => append(node, data)"
                  icon="el-icon-circle-plus-outline"
                >
                </el-button> -->
                <!-- <el-button
                  type="text"
                  v-show="node.level != 1"
                  icon="el-icon-delete"
                  @click="() => remove(node, data)"
                >
                </el-button> -->
                <el-button
                  v-if="node.data.addType == 1"
                  size="mini"
                  type="primary"
                  icon="el-icon-circle-plus-outline"
                  @click="() => appendField(node, data)"
                >
                  新增字段
                </el-button>
                <el-button
                  v-if="node.data.addType == 1 && node.level == 1"
                  size="mini"
                  type="success"
                  icon="el-icon-circle-plus-outline"
                  @click="() => append(node, data)"
                >
                  新增条件
                </el-button>
                <el-button
                  size="mini"
                  type="danger"
                  v-if="node.level != 1"
                  icon="el-icon-delete"
                  @click="() => open(node, data)"
                >
                  删除
                </el-button>
              </span>
            </div>
          </el-tree>
        </div>
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
let id = 1000;
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
    const data = [
      {
        id: 1,
        label: "一级",
        treeSelect: "1",
        addType: 1,
        children: [],
      },
    ];
    return {
      addZd: "", //新增字段
      isActive: "",
      active_id: "",
      alltreeData: [], //树状图组装提交
      addType: 0, //新增字段或条件
      treeData: JSON.parse(JSON.stringify(data)),
      active: 0,
      inputSS: "", //配置器搜索
      temp: {
        id: undefined,
        name: "",
        type: "",
        remark: "",
        dimension: ["选项1", "选项2"],
        dimension1: "选项1,选项1,选项1",
        field: "",
        method: "",
        timeNum: "",
        time: "",
      },
      varType: ["客户信息", "商户信息", "终端信息"],
      zdList: [
        {
          name: "商户名称",
          id: 1,
          zdId: 1,
          addType: 0,
          filed_1: "1",
          filed_2: "1",
        },
        {
          name: "商户状态",
          id: 2,
          zdId: 2,
          addType: 0,
          filed_1: "2",
          filed_2: "2",
        },
        {
          name: "集团商户标志",
          id: 3,
          zdId: 3,
          addType: 0,
          filed_1: "3",
          filed_2: "3",
        },
        {
          name: "法人证件号码",
          id: 4,
          zdId: 4,
          addType: 0,
          filed_1: "4",
          filed_2: "4",
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
      treeList: [
        {
          value: "1",
          label: "如下所有条件都成立",
        },
        {
          value: "2",
          label: "如下任一条件成立",
        },
        {
          value: "3",
          label: "如下所有条件都不成立",
        },
        {
          value: "4",
          label: "并且",
        },
        {
          value: "5",
          label: "或者",
        },
      ],
      filed_1: [
        {
          value: "1",
          label: "等于",
        },
        {
          value: "2",
          label: "大于",
        },
        {
          value: "3",
          label: "小于",
        },
        {
          value: "4",
          label: "大于等于",
        },
        {
          value: "5",
          label: "小于等于",
        },
        {
          value: "6",
          label: "为空",
        },
        {
          value: "7",
          label: "不为空",
        },
        {
          value: "8",
          label: "存在于",
        },
        {
          value: "9",
          label: "不存在于",
        },
      ],
      filed_2: [
        {
          value: "1",
          label: "常量",
        },
        {
          value: "2",
          label: "范围",
        },
        {
          value: "3",
          label: "正则表达式",
        },
        {
          value: "4",
          label: "基础变量",
        },
        {
          value: "5",
          label: "衍生变量",
        },
        {
          value: "6",
          label: "关联字段",
        },
        {
          value: "7",
          label: "自定义参数",
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
    var thisActive = this.$route.query.active;
    this.active = thisActive;
    this.temp.type = this.varType[0];
    this.temp.method = this.methodList[0].value;
    // this.temp.dimension = [this.dimensionList[0].value];
    this.temp.time = this.timeList[0].value;
  },
  methods: {
    // 获取字段列表信息
    getZd(item, index) {
      this.isActive = index;
      this.addZd = "";
      this.addZd = item;
      console.log(this.addZd);
    },
    // 监听tree下拉框
    seleChange(data) {
      console.log(data);
    },
    // 新增条件
    append(node, data) {
      const newChild = {
        id: id++,
        label: "条件",
        addType: 1,
        children: [],
        treeSelect: "1",
      };
      if (!data.children) {
        this.$set(data, "children", []);
      }
      data.children.push(newChild);
      console.log(data);
    },
    // 新增字段
    appendField(node, data) {
      if (this.addZd === "") {
        this.$alert("请从左侧选择字段", {
          confirmButtonText: "确定",
          callback: (action) => {
            // this.$message({
            //   type: "info",
            //   message: `action: ${action}`,
            // });
          },
        });
      } else {
        // const newChild = this.addZd;
        const newChild = {
          id: id++,
          zdId: this.addZd.zdId,
          name: this.addZd.name,
          addType: 0,
          filed_1: this.addZd.filed_1,
          filed_2: this.addZd.filed_2,
        };
        if (!data.children) {
          this.$set(data, "children", []);
        }
        data.children.push(newChild);
        console.log(data);
      }
    },
    // 确认删除
    open(node, data) {
      this.$confirm("此操作将删除该字段, 是否继续?", "提示", {
        confirmButtonText: "确定",
        cancelButtonText: "取消",
        type: "warning",
      })
        .then(() => {
          this.remove(node, data);
          this.$notify({
            title: "成功",
            message: "节点成功删除",
            type: "success",
          });
        })
        .catch(() => {});
    },
    // 删除节点
    remove(node, data) {
      const parent = node.parent;
      const children = parent.data.children || parent.data;
      const index = children.findIndex((d) => d.id === data.id);
      children.splice(index, 1);
      console.log(this.treeData);
    },
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
.el-icon-circle-check {
  display: none;
}
.active {
  .el-icon-circle-check {
    display: block;
  }
  .el-icon-circle-plus-outline {
    display: none;
  }
}
.custom-tree-node {
  display: flex;
  flex-direction: row;
}
.el-tree-node__content {
  height: auto;
}
.el-step__head.is-success {
  color: #009dff;
  border-color: #009dff;
}
.el-step__title.is-success {
  color: #009dff;
}
</style>
<style lang="scss" scoped>
.treeSelect {
  margin: 10px 0px;
}
.zdBox {
  width: 600px;
  height: 70px;
  margin-top: 10px;
  margin-bottom: 10px;
  border: 1px solid #ddd;
  border-radius: 5px;
  overflow: hidden;
  display: flex;
  flex-direction: column;
  .title {
    background-color: #009dff;
    width: 100%;
    height: 20px;
    color: #fff;
    font-size: 12px;
    line-height: 20px;
    padding-left: 10px;
  }
  .filed {
    display: flex;
    flex-direction: row;
    padding: 0px 10px;
  }
}
.treeText {
  margin-right: 5px;
}
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
    width: 20%;
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
          cursor: pointer;
          &.active {
            box-shadow: 1px 1px 5px rgba(0, 0, 0, 0.4);
            background-color: #009dff;
            color: #fff;
          }
          &:hover {
            box-shadow: 1px 1px 5px rgba(0, 0, 0, 0.4);
          }
          i {
          }
          p {
            margin: 0px;
          }
        }
      }
    }
  }
  .boxRight {
    width: 80%;
    padding: 15px;
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
