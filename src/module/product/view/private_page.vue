<template>
    <div>
        <div class="searcher">
            <div class="input-group">
                <rd-select :select=select1 @change=changeKey></rd-select>
                <input class="form-control" type="text" v-model="searchData.inputVal">
            </div>
            <button class="iconfont icon-search search-btn" @click="search"></button>
        </div>
        <hr>
        <h3 class="table-title">私有设备列表</h3>
        <table class="table">
            <thead>
                <tr>
                    <th>设备ID</th>
                    <th>接入时间</th>
                    <th>最近状态变更时间</th>
                    <th>状态</th>
                    <th>产品ID</th>
                    <th>产品名称</th>
                    <th>用户ID</th>
                    <th>操作</th>
                </tr>
            </thead>
            <tbody v-if="loading" class="loadingBg">
                <tr><td colspan="9"><rd-spin></rd-spin></td></tr>
            </tbody>
            <tbody>
                <tr v-if="noData"><td colspan="9">没有数据</td></tr>
                <tr v-for="col in tableData">
                    <td>{{col.device_id || '-'}}</td>
                    <td>{{col.accept_time || '-'}}</td>
                    <td>{{col.update_time || '-'}}</td>
                    <td v-if="col.online">在线</td><td v-else>离线</td>
                    <td>{{col.product_id || '-'}}</td>
                    <td>{{col.product_name || '-'}}</td>
                    <td>{{col.user_id || '-'}}</td>
                    <td>
                       <a role="button" class="tab" uid="{{col.device_id}}">查看详情</a>
                   </td>
                </tr>
            </tbody>
        </table>
        <pagin v-if="!noData" :page.sync="page"></pagin>
    </div>
</template>
<script>
    import { rdDatepicker, rdSpin, rdSelect } from 'radon-ui'
    import pagin from 'components/paging'
    var sData = {};
    export default {
      data() {
        return {
            noData: true,
            loading: false,
            tableData: [],
            searchData: {
                inputName: 'device_id',
                inputVal: '',
                formData: {
                    per_page: 10,
                    page: 1
                }
            },
            page: {
                total: 1,
                dataTotal: 1,
                cur: 1,
            },
            select1: {
                value   :{},
                options: [{
                    selected: true,
                    value: '设备ID',
                    val: 'device_id'
                }, {
                    selected: false,
                    value: '产品ID',
                    val: 'product_id'
                }, {
                    selected: false,
                    value: '产品名称',
                    val: 'product_name'
                }, {
                    selected: false,
                    value: '用户ID',
                    val: 'user_id'
                }]
            },
        }
      },
      watch: {
          'page.cur': function(val, oldVal) {
              sData['page'] = val;
              this.query(sData);
          }
      },
      components: {
          rdSpin,
          rdSelect,
          pagin
      },
      ready() {
        if(this.noData == true) {
            this.init();
        }
        
      },
      methods: {
        init: function() {
            sData = {
                per_page: 10,
                page: 1
            }
            this.query(sData);
        },
        changeSelect: function(select, value) {
            this.searchData.formData[select.key] = value.val;
        },
        changeKey: function(select, value) {
            this.searchData.inputName = value.val;
        },
        search: function() {
            let _this = this;
            sData = {};
            this.page.cur = 1;
            for(let i in this.searchData.formData) {
                if(typeof this.searchData.formData[i] !== "object") {
                    sData[i] = this.searchData.formData[i];
                }
            }
            if(this.searchData.inputVal.length > 0) {
                sData[this.searchData.inputName] = this.searchData.inputVal;
            }
            this.query(sData);
        },
        refreshTable: function(items) {
            this.tableData = [];
            for(var i in items) {
                this.tableData.$set(i, items[i]);
            }
        },
        query: function(fromData) {
            let _this = this;
            this.loading = true;
            $.ajax({
                url: "/device/selectPri",
                type: "get",
                dataType: "json",
                data: fromData,
                success: function(res) {
                    if(res.code == 500) {
                        _this.refreshTable();
                        _this.noData = true;
                        return;
                    }
                    _this.page.dataTotal = res.data.total_count;
                    _this.page.total = Math.ceil(res.data.total_count/10);
                    _this.refreshTable(res.data.items);
                    _this.noData = false;
                },
                complete: function() {
                    _this.loading = false;
                }
            })
        }
      }
    }
</script>
<style>
    .rd-modal {
        border-radius: 5px;
        overflow: hidden;
    }
    .rd-modal-body, .rd-modal-header {
        padding: 0.7rem;
    }
    .rd-modal-header {
        font-size: 14px;
        color: #333;
        background-color: #f0f1f5;
    }
    .searcher {
        padding: 10px 0;
    }
    .searcher .search-btn {
        width: 30px;
        height: 30px;
        padding: 0;
        background-color: #fff;
        outline: none;
        border: 1px solid #d9d9d9;
        color: #19bbff;
        border-radius: 4px;
        font-size: 14px;
    }
    .input-group {
        display: inline-block;
        margin-right: 60px;
        margin-top: 20px;
    }
    .input-group>*:nth-child(2) {
        margin-left: 10px;
    }
    .input-group>.rd-select-container {
        display: inline-block;
        width: 120px;
        vertical-align: middle;
    }
    .input-group>.form-control {
        display: inline-block;
        width: 180px;
        height: 32px;
        margin-top: 0;
        margin-bottom: 0;
        border-radius: 6px;
    }
    .audit>div {
        margin-top: 20px;
    }
    .audit>div span {
        display: inline-block;
        vertical-align: top;
        color: #666;
    }
    .audit span:nth-child(1) {
        width: 16%;
        text-align: right;
    }
    .audit span:nth-child(2) {
        width: 80%;
        margin-left: 3%;
    }
    .audit .form-control {
        margin: 0;
        height: 30px;
        width: 80%;
        font-size: 12px;
    }
    .audit textarea {
        width: 100%;
        height: 120px;
        font-size: 12px;
        padding: 10px 20px;
        resize: none;
        line-height: 1.42857143;
        color: #555;
        border: 1px solid #dcdee3; 
        border-radius: 4px;
        -webkit-transition: border-color ease-in-out .15s,-webkit-box-shadow ease-in-out .15s;
        -o-transition: border-color ease-in-out .15s,box-shadow ease-in-out .15s;
        transition: border-color ease-in-out .15s,box-shadow ease-in-out .15s;
    }
    .audit textarea:focus {
        border-color: rgba(65,216,255,1);
        outline: 0;
        -webkit-box-shadow: 0 0 8px rgba(65,216,255,0.5);
        box-shadow: 0 0 8px rgba(65,216,255,0.5);
    }
</style>