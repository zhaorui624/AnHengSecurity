<template>
    <div>
       <div class="card">
<!--           <div>{{this.tableData}}</div>-->
           <el-table
                   :data="tableData.filter(data => !search || data.name.toLowerCase())"
                   style="width: 1672px">
               <el-table-column
                       label="网卡名称"
                       prop="InterName">
               </el-table-column>
<!--               <el-table-column-->
<!--                        label="状态"-->
<!--                        prop="isup"-->
<!--               ></el-table-column>-->
               <el-table-column
                       label="速率"
                       prop="mtu">
               </el-table-column>
               <el-table-column
                       label="传输模式"
                       prop="duplex">
               </el-table-column>
               <el-table-column
                       label="IP地址"
                       prop="ip">
               </el-table-column>
               <el-table-column
                       label="子网掩码"
                       prop="netmask">
               </el-table-column>
               <el-table-column
                       align="right">
                   <template slot-scope="scope" >
                       <el-button
                               size="mini"
                               type="danger"
                               @click="handle(scope.row)"
                       >抓包</el-button>
                       <el-dialog title="" :visible.sync="outerVisible">
                           <el-form ref="form" :model="sizeForm" label-width="80px" size="mini">
                               <el-form-item label="网卡名称">
                                   <el-input v-model="sizeForm.InterName" disabled></el-input>
                               </el-form-item>
                               <el-form-item label="TCP/UDP">
                                   <el-select v-model="choose.region" placeholder="请选择传输模式 默认为TCP/UDP全选" style="width: 1110px">
                                       <el-option label="TCP" value="TCP" ></el-option>
                                       <el-option label="UDP" value="UDP"></el-option>
                                   </el-select>
                               </el-form-item>
                               <el-form-item label="输入数量">
                                   <el-input v-model="sizeForm.count" placeholder="默认为200"></el-input>
                               </el-form-item>
                               <el-form-item label="输入时间">
                                   <el-input v-model="sizeForm.to"  placeholder="默认为120"></el-input>
                               </el-form-item>
                           </el-form>
                           <div slot="footer" class="dialog-footer">
                               <el-button @click="outerVisible = false">取 消</el-button>
                               <el-button type="primary" @click="pack">立即抓包</el-button>
                           </div>
                       </el-dialog>
                   </template>
               </el-table-column>
           </el-table>
       </div>
<!--        <div>{{this.tableData}}</div>-->
    </div>
</template>

<script>
    // import {get} from "../../../api";
    // import {getPack} from "../../../api/network";
    import axios from 'axios'
    import {getCard} from "../../../api/network";
    // import {getStart} from "../../../api/network";
    export default {
        name: "card",
        data(){
            return {
                tableData: [{
                    InterName:'',
                    // broadcast:'',
                    // MAC:'',
                    isup:'',
                    description:'已经启动',
                    mtu:'',
                    duplex:'',
                    ip:'',
                    netmask:'',
                    // change:'修改'
                }],
                n:'',
                outerVisible: false,
                innerVisible: false,
                choose:{
                    region: '',
                    date1: '',
                    date2: '',
                },
                sizeForm: {
                    InterName: '',
                    count:'',
                    to:'',
                },
                message:''
                // data:''
            }
        },
        methods:{
            handle(row){
                this.outerVisible = true
                // console.log(row.InterName);
                this.sizeForm.InterName = row.InterName
            },
            pack(){
                if(this.sizeForm.count<0 || this.sizeForm.count>800||this.sizeForm.to<0||this.sizeForm.to>1800){
                    alert('输入数据有误！')
                }else {
                    axios.get('http://39.106.116.109:9095/api/package/startSniff', {
                        params: {
                            ifn:this.sizeForm.InterName,
                            f:this.choose.region,
                            count:this.sizeForm.count,
                            to:this.sizeForm.to
                        }
                    }).then(res=>{
                        // localStorage.setItem('start',res.toString());
                        console.log(res.data.data.filename);
                        this.message = res.data.data.filename
                        this.$emit('updateData',this.message)
                    })
                    this.$alert('<strong>开始抓包</strong>', '提示', {
                        dangerouslyUseHTMLString: true
                    });
                }
                this.outerVisible = false

                // let data = {
                //     city:val
                // }
                // // this.$emit('updateData', data)
            },

            // loading(){
            //     this.innerVisible = true
            //     const loading = this.$loading({
            //         lock: true,
            //         text: '正在加载',
            //         spinner: 'el-icon-loading',
            //         background: 'rgba(0, 0, 0, 0.7)'
            //     });
            //     setTimeout(() => {
            //         loading.close();
            //     }, 2000);
            // }
        },
        async created(){
            // //网卡的API
            const { data } = await getCard();
            this.tableData = data.data;
            // console.log(this.tableData)
            // console.log(data.data[this.n])
            // this.sizeForm = data.data[0];
            //抓包API
            // this.pack();
            // const res = await getStart();
            // console.log(res);
            // const res = await getPack();
            // this.data = res.data;
            // this.data = data1;
            // console.log(this.data);
        }
    }
</script>

<style scoped>
    .card{
       margin: 0 auto;
        /*border:5px solid green;*/
        /*border-radius: 5px;*/
        /*box-shadow:0 0 5px green;*/
        /*border:1px solid gray;*/
        background-color: white;
        color: black;
    }
    .el-input--mini .el-input__inner{
        height: 40px;
    }
    /*}*/
</style>