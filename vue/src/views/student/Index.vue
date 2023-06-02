<template>
    <main class="container">
        <div class="bg-light p-4 rounded mt-3">
            <h3>欢迎使用
            </h3>
            <p class="lead">现已开放预约</p>
            <a-table :dataSource="recommend" :columns="columns">
                <template #bodyCell="{ column, record }">
                <template v-if="column.key === 'set'">
                    <a-button type="primary" ghost @click="showModal(record.seat_id,record.room_id)">预约</a-button>
                </template>
                </template>
            </a-table>
        </div>
    </main>
    <a-modal v-model:visible="visible" title="选择时间" @ok="handleOK">
        <a-range-picker
                :show-time="{ format: 'HH' }"
                format="YYYY-MM-DD HH"
                :placeholder="['开始时间', '结束时间']"
                @change="onRangeOk"                                         
        />
    </a-modal>
</template>

<script>
import axios from 'axios';
import moment from 'moment';

export default {
    name: "index",
    date: [],
    data() {
        return {
            recommend: '',
            visible: false,
            seat_id:'',       //当前预约的座位号
            room_id:'',
            columns: [
                {
                    title: '座位id',
                    dataIndex: 'seat_id',
                    key: 'seat_id',
                },
                {
                    title: '位置',
                    dataIndex: 'pos',
                    key: 'pos',
                },
                {
                    title: '类型',
                    dataIndex: 'type',
                    key: 'type',
                },
                {
                    title: '状态',
                    dataIndex: 'state',
                    key: 'state',
                },
                {
                    title: '预约',
                    dataIndex: 'set',
                    key: 'set',
                },
            ],
        }
    },
    created () {
    this.getr()
    },
    methods: {
        //显示预约
        showModal(seat_id,room_id) {
            this.seat_id = seat_id
            this.room_id = 1 //room_id
            this.visible = true;
            console.log(seat_id,room_id)
        },

        async getr() {
            // 创建要发送的数据对象
            const data = {
                sid:1             //先写死，后面换成全局变量
            };
            //const params = qs.stringify(data);
            console.log("s data",data)
            
            // 发送请求(改)
            axios.post('http://localhost:8080/index/recommend/', data)      
                    .then(response => {
                        console.log("get recommend",response.data);
                        this.recommend = response.data.seat_list
                        })
        },

        
        onRangeOk(value,dateString) {                    //进行一定的调整
            console.log("time",value,dateString)
            this.date = dateString;
        },
        
        //和seat中匹配
        async handleOK() {
            console.log("????????")
            const startDateString = moment(this.date[0]).format('YYYY-MM-DD HH:mm:ss');
            const endDateString = moment(this.date[1]).format('YYYY-MM-DD HH:mm:ss');
            console.log("time",startDateString)
            const data = {
                room_id: parseInt(this.room_id),
                seat_id: parseInt(this.seat_id),
                start_time: startDateString,
                end_time: endDateString,
                sid:1            //学生的uid（理论上应该用个全局变量，先凑合一下吧，出错就改成测试账号的id）
            };
            console.log(data)
            try {
                //const response = await apiClient.post('/index/reservation/', data);
                //console.log(response.data); // 打印响应数据
                console.log("data:",data)
                //console.log(this.GLOBAL.uid)
                axios.post('http://localhost:8080/index/reservation/', data)      
                    .then(response => {
                        console.log(response.data); // 打印响应数据
                        let zhuangtai = '占用'

                        //this.seat_list[this.seat_id-1].state = zhuangtai
                        this.visible = false      //关闭窗口
                        })   
            } catch (error) {
                console.error(error); // 打印请求错误信息
            }
        }
        }

}
</script>

<style scoped>

</style>