<template xmlns:v-on="http://www.w3.org/1999/xhtml" xmlns:v-bind="http://www.w3.org/1999/xhtml" >
    <div style="width: 100%" >
        <el-row >
            <el-col :span="2" >
                <span >校车列表</span >
            </el-col >
            <el-col :span="3" :offset="16" >
                <el-button
		                icon="el-icon-download"
		                size="normal"
		                type="primary"
		                style="text-align: right;color: white;"
		                @click="onImport" >导入数据
                </el-button >
            </el-col >
            <el-col :span="3" >
                <el-button
		                icon="el-icon-upload2"
		                size="normal"
		                type="primary"
		                style="text-align: right"
		                @click="onExport" >导出数据
                </el-button >
            </el-col >
        </el-row >
        <el-row >
            <table style="margin-top: 10px;width: 100%;height: 100%;" >
                <tr style="height: 90px;" >
                    <td style="width: 220px;" >
                        <el-button

		                        icon="el-icon-plus"
		                        size="normal"
		                        type="primary"
		                        style="text-align: right"
		                        @click="onAdd" >添加校车
                        </el-button >
                    </td >
                    <td style="text-align: left" >
                        <el-row >
                            <el-col :span="1" >
                                <el-button
		                                class="speacial-button"
		                                icon="el-icon-delete"
		                                type="default"
		                                @click="onDeleteMore"
                                >
                                </el-button >
                            </el-col >
                            <el-col :span="14" :offset="6" >
                                <el-input v-model="queryKey"
                                          placeholder="输入关键字查询" clearable
                                          auto-complete="off" ></el-input >
                            </el-col >
                            <el-col :span="1" >
                                <el-button
		                                class="speacial-button"
		                                icon="el-icon-search"
		                                type="default"
		                                @click="search" >搜索
                                </el-button >
                            </el-col >

                        </el-row >
                    </td >
                </tr >
                <tr >
                    <td style="width: 220px;" >
                        <el-menu
		                        style="background-color: transparent;text-align: left;
                                vertical-align: text-top;
                                top: 0px;
                                margin-top: 0px;"
		                        :default-active="regionData.selectId" @select="handleSelect" >
                            <el-menu-item v-for="item in regionData.subList" :index="item.id"
                                          style="text-align: left;font-size: 14px; font-weight: bold" >
                                {{item.name}}校区
                            </el-menu-item >
                        </el-menu >
                    </td >
                    <td style="vertical-align: text-top;padding: 10px;" >
                        <el-table
		                        v-loading="loadingUI"
		                        element-loading-text="获取数据中..."
		                        :data="tableData"
		                        @selection-change="handleSelectionChange"
		                        :default-sort="{prop: 'number', order: 'descending'}"
		                        border
		                        highlight-current-row
		                        empty-text="暂无数据..."
		                        show-overflow-tooltip="true"
		                        style="width: 100%;" >
                            <el-table-column
		                            width="75"
		                            align="center"
		                            type="selection" >
                            </el-table-column >
                            <el-table-column
		                            align="center"
		                            prop="number"
		                            sortable
		                            label="校车" >
                            </el-table-column >
                            <el-table-column
		                            align="center"
		                            prop="busRangeName"
		                            sortable
		                            label="早/午区间" >
                                <template scope="scope" >
                                    <div >
                                        {{scope.row.busRangeName}}
                                    </div >
                                </template >
                            </el-table-column >
                            <el-table-column
		                            align="center"
		                            prop="busSupplierName"
		                            label="供应商" >
                                <template scope="scope" >
                                    <div >
                                        {{scope.row.busSupplierName}}
                                    </div >
                                </template >
                            </el-table-column >
                            <el-table-column label="BusMom"
                                             align="center"
                                             sortable
                                             prop="busMomName" >
                                <template scope="scope" >
                                    <div >
                                        {{scope.row.busMomName}}
                                    </div >
                                </template >
                            </el-table-column >
                            <el-table-column
		                            align="center"
		                            label="操作" >
                                <template scope="scope" >
                                    <el-tooltip placement="top" content="修改" >
                                        <el-button
		                                        size="mini"
		                                        type="primary"
		                                        icon="el-icon-edit"
		                                        @click="onUpdate(scope.row)" >
                                        </el-button >
                                    </el-tooltip >
                                    <el-tooltip placement="top" content="删除" >
                                        <el-button
		                                        size="mini"
		                                        type="success"
		                                        icon="el-icon-delete"
		                                        @click="onDelete(scope.row)" >
                                        </el-button >
                                    </el-tooltip >
                                </template >
                            </el-table-column >
                        </el-table >
                        <div class="block" style="text-align: center; margin-top: 20px" >
                            <el-pagination
		                            background
		                            @current-change="handleCurrentChange"
		                            :current-page="currentPage"
		                            :page-size="pageSize"
		                            layout="total, prev, pager, next, jumper"
		                            :total="totalRecords" >
                            </el-pagination >
                        </div >
                    </td >
                </tr >
            </table >
        </el-row >
        <el-dialog title="提示" :visible.sync="deleteConfirmDialog"
                   append-to-body width="30%" >
            <span >确认要删除选定的校车吗？</span >
            <span slot="footer" class="dialog-footer" >
              <el-button class="speacial-button" @click="deleteConfirmDialog = false"
                         icon="el-icon-close" >取 消</el-button >
              <el-button type="primary" @click="onConfirmDelete" icon="el-icon-check" >确 定</el-button >
            </span >
        </el-dialog >
    </div >
</template >

<script >
    var _this;
    import request from '../../api/request'

    export default {
	    name: "BusManage",
	    components: {},
	    data() {
		    _this = this;
		    return {
			    queryKey: '',
			    pageSize: EveryPageNum,//每一页的num
			    currentPage: 1,
			    startRow: 0,
			    totalRecords: 0,
			    loadingUI: false,
			    tableData: [],
			    multipleSelection: [],
			    regionData: {
				    selectId: "0",
				    selectName: '',
				    subList: RegionList,
			    },
			    deleteConfirmDialog: false,
			    selectedItem: {},
		    }
	    },
	    methods: {
		    handleSelectionChange(val) {
			    _this.multipleSelection = val;
		    },
		    handleCurrentChange(val) {
			    _this.currentPage = val;
			    _this.search();
		    },
		    onExport() {

		    },
		    onImport() {

		    },
		    onAdd() {

		    },
		    onDeleteMore() {
			    // _this.multipleSelection
			    if (!_this.multipleSelection || _this.multipleSelection.length == 0) {
				    showMessage(_this, "请选择要删除的数据！");
				    return;
			    }
		    },
		    onUpdate(row) {
			    _this.selectedItem = row;
		    },
		    onDelete(row) {
			    _this.selectedItem = row;
			    _this.deleteConfirmDialog = true;
		    },
		    onConfirmDelete() {
			    _this.deleteConfirmDialog = false;
			    let params = new URLSearchParams();
			    params.append('id', _this.selectedItem.id);
			    request({
				    url: `${HOST}bus/base/info/delete`,
				    method: 'post',
				    data: params
			    }).then(response => {
				    showMessage(_this, "删除成功！")
				    let index = _this.tableData.indexOf(_this.selectedItem);
				    _this.tableData.splice(index, 1);
				    _this.selectedItem = {}
			    }).catch(error => {
				    showMessage(_this, "删除失败！")
			    })
		    },
		    search() {
			    _this.loadingUI = true;
			    let params = new URLSearchParams();
			    let condition = {
				    "keyWord": _this.queryKey,
				    page: _this.currentPage,
				    size: _this.pageSize,
				    schoolPartition: _this.regionData.selectName,
			    }
			    if (condition) {
				    let keys = Object.keys(condition);
				    for (let key of keys) {
					    params.append(key, condition[key]);
				    }
			    }
			    request({
				    url: `${HOST}bus/base/info/getBusBaseInfo`,
				    method: 'post',
				    data: params
			    }).then(res => {
				    if (res.data.code == 200) {
					    _this.tableData = res.data.data.list;
					    _this.totalRecords = res.data.data.total;
					    _this.startRow = res.data.data.startRow;
				    }
				    else {
					    //showMessage(_this,"获取数据失败！");
				    }
				    _this.multipleSelection = [];
				    _this.selectedItem = {};
				    _this.loadingUI = false;

			    }).catch(error => {
				    showMessage(_this, error)
				    _this.loadingUI = false;
			    })
		    },

		    //左侧区域
		    handleSelect(key) {
			    if (_this.regionData.selectId == key) {
				    return;
			    }
			    _this.regionData.selectId = key;
			    console.log(_this.regionData.selectId)
			    for (let i = 0; i < _this.regionData.subList.length; i++) {
				    if (_this.regionData.subList[i].id == key) {
					    _this.regionData.selectName = _this.regionData.subList[i].name
					    break;
				    }
			    }
			    _this.search();//refresh
		    },
	    },
	    computed: {},

	    filters: {},

	    created: function () {


	    },
	    mounted: function () {
		    _this.search();
	    }
    }
</script >
<style >
    .el-button {
	    color: #ffffff;
    }

    .speacial-button {
	    color: #bfcbd9;
    }

    /*tr {*/
    /*border: #bfcbd9 1px solid;*/
    /*text-align: center*/
    /*}*/

    td {
	    border: #bfcbd9 1px solid;
	    text-align: center
    }
</style >
