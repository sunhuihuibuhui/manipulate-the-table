<template>
  <section>
    <div class="title">
      <h2>XX超市库存表</h2>
      <el-button type='success' class="el-icon-plus" circle @click="addItem()"></el-button>
    </div>
    <el-table :data="tableData.list"  border highlight-current-row>
      <el-table-column label="序号" type="index" width="60"></el-table-column>
      <el-table-column v-for="i in tableData.basic" :key='i.id' :prop="i.prop" :label="i.title">
        <template slot-scope="scope">
          <el-input v-if="scope.row.ischecked" v-model="tableData.checked[i.prop]"> </el-input>
          <span v-else>{{scope.row[i.prop]}}</span>
        </template>
      </el-table-column>
      <el-table-column label="操作" >
        <template slot-scope="scope">
          <el-button :type='scope.row.ischecked ? "success" : "primary"' @click = "editItem(scope.row,scope.$index,true)">
            {{scope.row.ischecked?'保存':"修改"}}
          </el-button>
          <el-button type='danger' v-if="!scope.row.ischecked" @click  ="delItem(scope.$index)">删除</el-button>
          <el-button v-else @click = "editItem(scope.row,scope.$index,false)">取消</el-button>
        </template>
      </el-table-column>
    </el-table>
  </section>
</template>

<script>
  export default {
    data() {
      return {
        tableData: {
            checked: null,//选中行   
            basic: [//table的表头和参数名
              { prop: "name", title: "名称", id:1 },
              { prop: "number", title: "数量", id:2 },
            ],
            list: [
              {"name":"雨伞","number":"10","id":"3","ischecked":false},
              {"name":"台灯","number":"16","id":"4","ischecked":false},
              {"name":"玩偶","number":"22","id":"5","ischecked":false},
            ],
        },
      }
    },
    methods: {
      //创建uuid
      guid(){
        let S4 = ()=>{
          return (((1+Math.random())*0x10000)|0).toString(16).substring(1);
        };
        return (S4()+S4()+"-"+S4()+"-"+S4()+"-"+S4()+"-"+S4()+S4()+S4());
      },

      //添加
      addItem() {
        for (let i of this.tableData.list) {//是否已经保存其它操作
          if (i.ischecked) return this.$message.warning("请先保存当前编辑项");
        };
        //创建空白条目并选中
        this.tableData.list.push({"name": "", "number": "", "ischecked": true });
        this.tableData.checked = JSON.parse(JSON.stringify({"name": "", "number": "", "ischecked": true }));
      },

      //操作
      editItem(row, index, save) {
        for (let i of this.tableData.list) {//是否已经保存其它操作
          if (i.ischecked && i.id != row.id) return this.$message.warning("请先保存当前编辑项");
        };
        if (!save) {//点击取消
          if (!this.tableData.checked.id){ //判断新增还是修改
            this.tableData.list.splice(index, 1);
          };
          return row.ischecked = !row.ischecked;
        };
        if (row.ischecked) {//点击保存
          for(var i in this.tableData.checked){//要求信息填写完整
            if(!this.tableData.checked[i]) return this.$message.warning('请先完善信息');
          };
          if(!this.tableData.checked.id){//如果是新增的条目，就赋给一个uuid
            this.tableData.checked.id = this.guid();
          };
          let newItem = JSON.parse(JSON.stringify(this.tableData.checked));
          for (let i in newItem) row[i] = newItem[i];
          row.ischecked = false;
        } else {//点击修改
          row.ischecked = true;
          this.tableData.checked = JSON.parse(JSON.stringify(row));
        }
      },

      //删除
      delItem(ind){
        this.tableData.list.splice(ind,1);
      },

    }
  }
</script>

<style lang="scss" scoped>
section{
  width: 600px;
  margin: 50px auto;
  .title{
    height: 70px;
    position: relative;
    button{
      position: absolute;
      right: 10px;
      bottom: 10px;
    }
  }
}
</style>

