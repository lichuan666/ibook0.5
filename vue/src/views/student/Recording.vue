<template>
    <main class="container">
        <div class="row">
            <div class="col-3 mt-3 bg-light" v-for="reservation in reservation_list">
                <div class="p-4 rounded mt-3 h-100">
                    <h5>座位号：{{ reservation.seat_id }}</h5>
                    <h5>开始时间：{{ reservation.start_time }}</h5>
                    <h5>结束时间：{{ reservation.end_time }}</h5>
                    <h5>状态：{{ reservation.status == 1 ? '已签到':'未签到' }}</h5>

                    <p class="lead text-center">请保持良好的学习环境！</p>
                    <p class="text-center">
                        <a-button type="button" @click="sign(reservation.seat_id,reservation.create_time)">签到</a-button>
                        <a type="button" class="btn btn-outline-success" data-bs-toggle="modal"
                           data-bs-target="#exampleModal-1" @click="choooser(reservation.id)">取消预约</a>
                    </p>


                    <!-- Button trigger modal -->

                    <!-- Modal 原版-->
                    <!---->
                    <div class="modal fade" id="exampleModal-1" tabindex="-1"
                         aria-labelledby="exampleModalLabel"
                         aria-hidden="true">
                        <div class="modal-dialog">
                            <div class="modal-content">
                                <div class="modal-header">
                                    <h5 class="modal-title" id="exampleModalLabel">请确认一下信息！</h5>
                                    <button type="button" class="btn-close" data-bs-dismiss="modal"
                                            aria-label="Close"></button>
                                </div>
                                <div class="modal-body">
                                    <p class="h5">你确定取消吗？</p>


                                </div>
                                <div class="modal-footer">
                                    <button type="button" class="btn btn-secondary" data-bs-dismiss="modal">
                                        取消
                                    </button>
                                    <a class="btn btn-danger" @click="cancel" data-bs-dismiss="modal">确定</a>
                                </div>
                            </div>
                        </div>
                    </div>
                    
                </div>
            </div>
        </div>
        <p class="lead text-center">历史预定情况</p>
        <div class="row">
            <div class="col-3 mt-3 bg-light" v-for="reservation in reservation_list">
                <!--历史记录-->
                
                <div class="p-4 rounded mt-3 h-100">
                    <h5>座位号：{{ reservation.seat_id }}</h5>
                    <h5>开始时间：{{ reservation.start_time }}</h5>
                    <h5>结束时间：{{ reservation.end_time }}</h5>
                    <p class="text-center">
                        <a type="button" class="btn btn-outline-success" data-bs-toggle="modal"
                           data-bs-target="#exampleModal-1" @click="again(reservation.id)">再次预约</a>
                    </p>
                </div>
            </div>
        </div>    
        
    </main>
    <!--2.0
    <a-modal v-model:visible="visible" title="请再次进行确认" @ok="cancel">
        <h5>是否需要取消预约</h5>
    </a-modal>
    -->
</template>

<script>
import axios from 'axios';
import router from "@/router";
import moment from 'moment';

const apiClient = axios.create({
    baseURL: 'http://127.0.0.1:8080/',
});
apiClient.interceptors.request.use(config => {
    config.headers['token'] = localStorage.getItem('token');
    config.headers['username'] = localStorage.getItem('username');
    return config;
});


export default {
    name: "Recording",
    data() {
        return {
            rid:'',       //选择的预约记录
            reservation_list: [
                /*
                {
                    "seat_id": 1,
                    "start_time": "2023-04-15T21:19:56.824734",
                    "end_time": "2023-04-15T22:19:56.824734",
                    "status": "2"
                },
                {
                    "seat_id": 4,
                    "start_time": "2023-04-14T10:00:00",
                    "end_time": "2023-04-14T11:00:00",
                    "status": "1"
                }
                */
            ]
        }
    },
    
    created () {
    this.getr()
    },

    methods: {
        choooser(id) {
            //console.log("Recording cancel")
            console.log("value",id)
            this.rid = id;
            this.visible = false
        },

        async cancel() {
            let rid = this.rid
            const data = {
                rid:rid,             
            };
            // 发送请求(改)
            axios.post('http://localhost:8080/index/cancel/', data)      
                    .then(response => {
                        console.log("cancel",response.data)
                        //this.hidden = false
                        //this.visible = true
                        console.log("cancel",this.hidden)
                        this.getr()         //刷新页面
                        })   
        },

        //再次预约
        async again() {
        },

        async sign(seat_id,create_time) {
            // 创建要发送的数据对象
            let time  = new Date()
            time = moment(time).format('YYYY-MM-DD HH:mm:ss');
            const data = {
                sid:1,             //先写死，后面换成全局变量
                time:time,
                seat_id:seat_id,
                createtime:create_time
            };
            console.log("sign",data)
            
            // 发送请求(改)
            axios.post('http://localhost:8080/index/sign/', data)      
                    .then(response => {
                        console.log("sign",response.data);
                        })
        },

        async getr() {
        try {
            const data = {
                sid:1,             //先写死，后面换成全局变量
                method:'getall',
            };
            //const response = await apiClient.get('/index/view_reservation/', {});
            // 发送请求(改)
            axios.post('http://localhost:8080/index/view_reservation/', data)      
                    .then(response => {
                        console.log("sign",response.data);
                        this.reservation_list = response.data.reservation_list
                        })
            
        } catch (error) {
            console.error(error); // 打印请求错误信息
        }
        },
    }
    
}
</script>

<style scoped>

</style>