<template>
<div class="content-page" id="msg-wall">
    <div class="content links-page">

        <div class="row wall">
            <div class="col-lg-12">
                <div class="card">
                    <div class="card-body">
                        <div class="flex-center">
                            <svg-icon v-on:click="methods.addComment()" i-class="1-5em" file-name="plus" class="mr-1 cursor"></svg-icon>
                            <span v-on:click="methods.addComment()" class="cursor">写一张随心贴，留下想说的话</span>
                        </div>
                    </div>
                </div>
            </div>
            <div v-for="data in msg_wall.data" :key="data.id" class="col-md-4">
                <div :class="'card text-white text-xs-center ' + (data.opt.bg || 'bg-primary')">
                    <div class="card-body">
                        <blockquote class="card-bodyquote mb-0">
                            <img :src="data.expand.head_img || null" height="42" width="42" class="rounded-circle shadow-sm head-img">
                            <span class="nickname">{{data.nickname || '用户'}}</span>
                            <p class="mb-1 mt-1 msg-wall-scroll">{{data.content || '评论内容'}}</p>
                            <div class="flex mb-1">
                                <span class="text-white float-left">{{methods.natureTime(data.create_time)}}</span>
                                <span class="text-white float-right">
                                    <svg-icon v-on:click="methods.like(data.id)" v-if="!like.includes(data.id)" i-class="1-5em" file-name="like-1" class="mr-1 cursor"></svg-icon>
                                    <svg-icon v-on:click="methods.notice()" v-if="like.includes(data.id)" i-class="1-5em" file-name="like-2" class="mr-1 cursor"></svg-icon>
                                    <svg-icon v-on:click="methods.comments(data.id)" i-class="1-8em" file-name="comment" class="cursor"></svg-icon>
                                </span>
                            </div>
                            <div v-if="show_comments == data.id" class="row">
                                <div class="card-body pl-2 pr-2 pt-0 pb-2">
                                    <div class="input-group">
                                        <input v-model="add_msg_wall.comment" type="text" class="form-control" placeholder="请说点什么吧~">
                                        <div class="input-group-append">
                                            <button v-on:click="methods.saveMsgWall('comment')" class="btn btn-dark" type="button">提交</button>
                                        </div>
                                    </div>
                                </div>
                            </div>
                            <div class="card mb-0">
                                <div class="card-body p-2">
                                    <p class="m-0 flex">
                                        <span class="float-left">
                                            <svg-icon i-class="1-5em" file-name="like-3" class="mr-1"></svg-icon>
                                            {{data.opt.great || 0}}
                                        </span>
                                        <span class="float-right">

                                        </span>
                                    </p>
                                    <div class="msg-wall-scroll">
                                        <p v-for="item in data.son" :key="item.id" class="m-0">{{item.nickname || '回复的用户'}}：{{item.content || '回复的内容'}}</p>
                                    </div>
                                </div>
                            </div>
                        </blockquote>
                    </div>
                </div>
            </div>
            <div class="col-lg-12 article-footer">
                <div class="card-body pt-0">
                    <div class="flex-center">
                        <span v-show="page.msgWall.code == msg_wall.page" class="see-more pt-1 pb-1 pl-3 pr-3">再怎么找也没有啦~</span>
                        <span>
                            <button v-show="page.msgWall.code != msg_wall.page" v-on:click="methods.getMsgWall(page.msgWall.code + 1)" type="button" class="btn btn-link text-muted pt-1 pb-1 pl-3 pr-3 see-more">查看更多</button>
                        </span>
                    </div>
                </div>
            </div>
        </div>

        <div class="row">
            <div class="col-lg-8">
                <div class="card">
                    <div class="card-body">

                        <h4 class="header-title mb-3">总评论排行榜</h4>

                        <div v-if="page.comments.load" class="card-body flex-center">
                            <div class="spinner-border text-primary m-1" role="status"></div>
                            <div>加载中...</div>
                        </div>
                        <div v-else-if="!page.comments.load" class="row">
                            <div v-for="data in comments.data" :key="data.id" class="col-md-6">
                                <a :href="'//' + data.url || 'javasctipt:;'" target="_blank">
                                    <div class="card">
                                        <div class="card-body">
                                            <div class="media">
                                                <img :src="data.expand.head_img || null" class="mr-3 rounded-circle" width="40" height="40">
                                                <div class="media-body">
                                                    <h5 class="mt-0 mb-1 text-dark">{{data.nickname || '友链名称'}}</h5>
                                                    <span class="font-13 text-muted text-line line-limit-1">{{data.url || '这个人很懒，什么都没留下！'}}</span>
                                                </div>
                                                <span :class="'badge ' + methods.randomClass()">{{data.count}}</span>
                                            </div>
                                        </div>
                                    </div>
                                </a>
                            </div>
                        </div>
                        <div class="flex space-between align-items-center mt-2">
                            <div class="d-none d-md-block"> 共 {{comments.page || 0}} 页 {{comments.count || 0}} 条数据 </div>
                            <div class="btn-group">
                                <button v-on:click="methods.getComments(null, 1)" type="button" class="btn btn-light btn-sm"><svg-icon file-name="primary-left"></svg-icon></button>
                                <button v-for="(item, index) in page.comments.list" :key="index" v-on:click="methods.getComments(null, item)" :class="'btn btn-sm ' + ((page.comments.code == item) ? 'btn-primary' : 'btn-light') ">
                                    {{item}}
                                </button>
                                <button v-on:click="methods.getComments(null, comments.page)" type="button" class="btn btn-light btn-sm"><svg-icon file-name="primary-right"></svg-icon></button>
                            </div>
                        </div>
                    </div>
                </div>
            </div>

            <div class="col-lg-4">
                <div class="card">
                    <div class="card-body">

                        <h4 class="header-title">90天内评论排行</h4>

                        <div class="flex-center">
                            <canvas id="comments-chart"></canvas>
                        </div>

                        <div class="chart-widget-list">
                            <p v-for="(data,index) in ranking" :key="index">
                                {{data.name}}
                                <span class="float-right">{{data.value}}</span>
                            </p>
                        </div>
                    </div>
                </div>
            </div>
        </div>

    </div>
    <teleport to="body">
    <div id="fill-primary-modal" class="modal fade" tabindex="-1" role="dialog" aria-labelledby="fill-primary-modalLabel" aria-hidden="true" style="display: none;">
        <div class="modal-dialog">
            <div class="modal-content modal-filled bg-primary">
                <div class="modal-header">
                    <h4 class="modal-title" id="fill-primary-modalLabel">添加随心贴</h4>
                    <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
                </div>
                <div class="modal-body">
                    <div class="card-body pb-0">
                        <div class="row">
                            <div class="col-lg-12">

                                <div class="form-group row">
                                    <label>背景颜色</label>
                                    <div class="col-md-9">
                                        <div class="btn-group mb-2">
                                            <div class="btn-group">
                                                <button type="button" class="btn btn-light dropdown-toggle" data-toggle="dropdown" aria-expanded="false">
                                                    {{msg_wall_title}}
                                                    <span class="caret"></span>
                                                </button>
                                                <div class="dropdown-menu">
                                                    <a v-on:click="methods.setMsgWall({bg:'bg-secondary'})" class="dropdown-item flex-center" href="javascript:;">
                                                        <span class="bg-secondary msg-bg mr-2"></span>
                                                        黑色
                                                    </a>
                                                    <a v-on:click="methods.setMsgWall({bg:'bg-primary'})" class="dropdown-item flex-center" href="javascript:;">
                                                        <span class="bg-primary msg-bg mr-2"></span>
                                                        紫色
                                                    </a>
                                                    <a v-on:click="methods.setMsgWall({bg:'bg-success'})" class="dropdown-item flex-center" href="javascript:;">
                                                        <span class="bg-success msg-bg mr-2"></span>
                                                        绿色
                                                    </a>
                                                    <a v-on:click="methods.setMsgWall({bg:'bg-info'})" class="dropdown-item flex-center" href="javascript:;">
                                                        <span class="bg-info msg-bg mr-2"></span>
                                                        蓝色
                                                    </a>
                                                    <a v-on:click="methods.setMsgWall({bg:'bg-warning'})" class="dropdown-item flex-center" href="javascript:;">
                                                        <span class="bg-warning msg-bg mr-2"></span>
                                                        黄色
                                                    </a>
                                                    <a v-on:click="methods.setMsgWall({bg:'bg-danger'})" class="dropdown-item flex-center" href="javascript:;">
                                                        <span class="bg-danger msg-bg mr-2"></span>
                                                        红色
                                                    </a>
                                                </div>
                                            </div>
                                        </div>
                                    </div>
                                </div>
                            
                                <div class="form-group row">
                                    <label>内容</label>
                                    <textarea v-model="add_msg_wall.content" class="form-control msg-textarea" rows="5" placeholder="随心贴：留下您想说的话！"></textarea>
                                </div>
                            </div>
                        </div>
                    </div>
                </div>
                <div class="modal-footer">
                    <button v-on:click="methods.saveMsgWall()" type="button" class="btn btn-outline-light">提交评论</button>
                </div>
            </div>
        </div>
    </div>
    </teleport>

    <i-footer></i-footer>
</div>
</template>

<script>
import * as echarts from 'echarts'
import { GET, POST } from '@/utils/http/request'
import { Get } from '@/utils/http/fetch'
import iFooter from '@/components/public/footer'
import { onMounted, reactive, toRefs } from 'vue'
import { inisHelper } from '@/utils/helper/helper'
import { useStore } from 'vuex'

export default {
    components: { iFooter },
    setup(){

        const state = reactive({
            comments: [],    // 友链数据
            links_chart: [], // 友链统计
            ranking: [],     // 规定时间内的排行 - 排序前
            year: (new Date).getFullYear(), // 本年
            is_login: false, // 是否登录
            msg_wall:{       // 随心贴数据
                data:[]
            },
            add_msg_wall:[], // 添加随心贴的内容
            msg_wall_title:"紫色", // 默认随心贴颜色
            login_storage: [],     // 登录缓存
            show_comments:null,    // 显示评论
            like:[],               // 点赞
            page:{
                comments:{
                    code:1,
                    list:[],
                    show:false,
                    load:false
                },
                msgWall: {
                    code:1
                }
            }
        })

        // Vuex 响应实例
        const store = useStore()

        // 获取缓存中的点赞记录
        let like_storage = inisHelper.get.storage("like", "msg-wall")
        // 判断缓存是否存在
        if (like_storage != false) {
            for (let item in like_storage) state.like.push(like_storage[item])
        }

        const methods = {
            // 初始化数据
            initData(){
                methods.checkLogin()
                methods.getComments()
                methods.getMsgWall()
            },
            getComments(id = null, page = state.page.comments.code){

                state.page.comments.load = true
                
                const params = {limit:8,page}

                GET('comments/group', {params}).then(res=>{
                    if (res.data.code == 200) {
                        const result   = res.data.data
                        state.comments = result

                        // 是否显示分页
                        if (inisHelper.is.empty(result.data) || result.page == 1) state.page.comments.show = false
                        else state.page.comments.show = true
                        
                        // 更新页码
                        state.page.comments.code = page
                        
                        // 页码列表
                        state.page.comments.list = inisHelper.create.paging(page, result.page, 5)
                        // 加载动画
                        state.page.comments.load = false
                        // 设置页面 title
                        document.title = '留言墙 - ' + store.state.theme_config.basic.site.title
                    }
                })

                // 七天内时间戳
                const time = Math.round(new Date / 1000) - 86400 * 90
                Get('comments/sql',{
                    where: [
                        ['create_time', '>', time]
                    ],
                    limit:99999
                }).then(res=>{
                    if (res.code == 200) {
                        methods.process(res.data.data)
                    }
                })
            },
            // 绘图前处理数据
            process(items){
                let result = []
                for(let i = 0; i < items.length;) {
                    let count = 0;
                    items.forEach((item)=>{
                        if(items[i].email == item.email) count++;
                    })
                    let obj = {
                        name:items[i].nickname,
                        value:count,
                        email:items[i].email
                    }
                    result.push(obj)
                    i += count;
                }
                state.ranking = result
                methods.echarts()
            },
            // 人性化时间
            natureTime(date = null){
                const time = inisHelper.date.to.time(date)
                return inisHelper.time.nature(time)
            },
            // 随机 class
            randomClass(){
                let array = ['badge-primary-lighten','badge-secondary-lighten','badge-success-lighten','badge-danger-lighten','badge-warning-lighten','badge-info-lighten','badge-dark-lighten'];
                return array[parseInt(Math.random() * array.length)]
            },
            // XX天内评论排行绘图
            echarts(){
                let chart = echarts.init(document.querySelector("#comments-chart"));
                chart.setOption({
                    series : [{
                        name: '评论排行',
                        type: 'pie',
                        radius: '60%',
                        data: state.ranking,
                    }]
                })
                // 降序排序
                state.anking = inisHelper.array.sort.two(state.ranking,'value','desc')
            },
            getVirtulData(year = state.year){
                let date = +echarts.number.parseDate(year + '-01-01');
                let end = +echarts.number.parseDate((+year + 1) + '-01-01');
                let dayTime = 3600 * 24 * 1000;
                let data = [];
                for (let time = date; time < end; time += dayTime) {
                    data.push([
                        echarts.format.formatTime('yyyy-MM-dd', time),
                        Math.floor(Math.random() * 1000)
                    ]);
                }
                return data;
            },
            // 添加随心贴
            addComment(){
                methods.checkLogin()
                if (state.is_login) {
                    // 默认随心贴背景色
                    state.add_msg_wall = {
                        bg: "bg-primary",
                        token: state.login_storage['login-token']
                    }
                    $("#msg-select2").select2({
                        minimumResultsForSearch: -1
                    })
                    $('#fill-primary-modal').modal('show')
                } else {
                    $.NotificationApp.send(null, '请先登录！', "top-right", "rgba(0,0,0,0.2)", "warning")
                }
            },
            // 提交前设置随心贴内容
            setMsgWall(obj = {}){
                let bg_color = {
                    "bg-secondary" :  "黑色",
                    "bg-primary"   :  "紫色",
                    "bg-success"   :  "绿色",
                    "bg-info"      :  "蓝色",
                    "bg-warning"   :  "黄色",
                    "bg-danger"    :  "红色",
                }
                for (let item in obj) {
                    if (item == "bg") {
                        state.add_msg_wall.bg = obj[item]
                        state.msg_wall_title = bg_color[obj[item]]
                    }
                }
            },
            // 提交随心贴
            saveMsgWall(type = 'add', id = null){
                
                let params = {}
                let check  = false

                if (type == 'add') {

                    params = {
                        "login-token" : state.add_msg_wall.token,
                        "content"     : state.add_msg_wall.content,
                        "opt"         : {bg:state.add_msg_wall.bg, great:1},
                        "type"        : "msg_wall"
                    }

                    if (inisHelper.is.empty(state.add_msg_wall.content)) $.NotificationApp.send("提示！", '请说点什么再提交随心贴！', "top-right", "rgba(0,0,0,0.2)", "warning")
                    else check = true
                    
                } else if (type == 'comment') {
                    params = {
                        "login-token" : state.add_msg_wall.token,
                        "content"     : state.add_msg_wall.comment,
                        "type"        : "msg_wall",
                        "pid"         : state.add_msg_wall.pid
                    }
                    if (inisHelper.is.empty(state.add_msg_wall.comment)) $.NotificationApp.send("提示！", '请说点什么吧！', "top-right", "rgba(0,0,0,0.2)", "warning")
                    else check = true
                } else if (type == 'like') {
                    params = {id,mode:'like'}
                    check  = true
                }

                if (check) POST('comments', params).then(res=>{
                    if (res.data.code == 200) {
                        // 关闭添加随心贴
                        $('#fill-primary-modal').modal('hide')
                        // 更新数据
                        methods.getMsgWall()
                        // 清除评论框
                        state.show_comments = null
                        state.add_msg_wall = {}
                    }
                })
            },
            // 获取随心贴
            getMsgWall(page = state.page.msgWall.code){
                const params = {
                    type:"msg_wall",
                    tree: false,
                    limit: 6,
                    page,
                    order:"desc"
                }
                GET('comments/type',{params}).then(res=>{
                    if (res.data.code == 200) {
                        const result   = res.data.data
                        state.msg_wall.page = result.page
                        state.msg_wall.count= result.count
                        state.msg_wall.data = [...state.msg_wall.data, ...result.data]
                        state.page.msgWall.code = page
                    }
                })
            },
            // 设置评论数据
            comments(id = null){
                methods.checkLogin()
                if (state.is_login) {
                    if (id == state.show_comments) {
                        state.show_comments = null
                        state.add_msg_wall.pid = null
                        state.add_msg_wall.comment = null
                    } else {
                        state.show_comments = id
                        state.add_msg_wall.pid = id
                        state.add_msg_wall.token = state.login_storage['login-token']
                    }
                } else {
                    $.NotificationApp.send(null, '请先登录！', "top-right", "rgba(0,0,0,0.2)", "warning")
                }
            },
            // 点赞
            like(id = null){
                state.like.push(id)
                // 往缓存中记录点赞数据 - 想重复点赞？不可能，当然，想取消点赞也是不可能的 ʚ♡⃛ɞ(ू•ᴗ•ू❁)
                inisHelper.set.storage("like","msg-wall", state.like)
                // 执行点赞
                methods.saveMsgWall('like', id)
            },
            // 通知
            notice(mode = 'like', type = 'info'){
                if (mode == 'like') $.NotificationApp.send("提示！", '请不要重复点赞！', "top-right", "rgba(0,0,0,0.2)", type)
            },
            checkLogin(){
                // 获取缓存中的登录信息
                let login_storage = inisHelper.get.storage("login")
                // 判断缓存是否存在且未过期
                if (login_storage != "expire" && login_storage != false) {
                    state.login_storage = login_storage
                    state.is_login = true
                } else state.is_login = false
            }
        }

        onMounted(()=>{
            methods.initData()
        })

        return { ...toRefs(state), methods }
    },
    computed: {
        
    }
}
</script>

<style scoped>
.charts {
  width: 80%;
  height: 200px;
}
</style>