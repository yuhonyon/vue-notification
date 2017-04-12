<template>
        <div v-if="show" class="Notification-wrap">
            <div :class="classes" v-for="info in infos" :key="info.id" @click="clickHandle(info)" @show="show(info)">
                <div class="Notification-icon">
                    <img :src="info.icon">
                </div>
                <div class="Notification-content">
                    <h4>{{info.title}}</h4>
                    <p>{{info.content}}</p>
                </div>
                <span class="Notification-close" @click.stop="close(info)">X</span>
            </div>
        </div>
</template>
<script>
export default {


    data() {
            return {
                show: false,
                request:false,
                curInfos:{},
                noti:[]

            }
        },
        created() {
            if(!this.isPermissionGranted()){
                this.requestPermission();
            }else{
                this.request=true;
            }


            window.onbeforeunload=()=>{
                for (let n of this.noti){
                    n.close();
                }
            }
        },

        props: {
            infos: null,
        },
        watch: {
            infos() {
                this.showNotification()
            },
            request(){
                this.showNotification();
            }
        },

        computed: {
            classes() {
                return ['Notification', 'animated']
            },
            isNotificationSupported() {
                return 'Notification' in window
            },

            
        },
        methods: {
            show(info){
                this.$emit('show',info);
            },
            close(info){
                this.$emit('close',info);
            },

            clickHandle(info){
                this.$emit('clickHandle',info);
            },

            isPermissionGranted() {
                return this.isNotificationSupported?(Notification.permission === 'granted'):false;
            },
            
            requestPermission: function() {
                if (!this.isNotificationSupported) {
                    return;
                }
                Notification.requestPermission(() => {
                    this.request=true;
                });
            },
            showNotification() {
                if (!this.request && this.isNotificationSupported) {
                    return;
                }

                this.$emit('show',this.infos);

                if (!this.isNotificationSupported||!this.isPermissionGranted() ) {
                    this.NotiError();
                    return;
                }

                this.Notisuccess();

            },
            Notisuccess() {

                for(let n of this.noti){
                    if(typeof (this.infos.find((value)=>{return value.id==n.id}))=="undefined"){
                        n.close()
                    }
                }

                for (let info of this.infos) {
                    if(this.curInfos[info.id]){
                        continue;
                        return;
                    }

                    this.curInfos[info.id]=true;
                    let n= new Notification(info.title, {
                        icon: info.icon,
                        body: info.content
                    });
                    n.id=info.id;
                    n.onclick = () => {
                        this.clickHandle(info);
                        n.close();
                    };
                    n.onclose = () => {
                        this.close(info);
                    };

                    this.noti.push(n);
                }

            },
            NotiError() {
                this.show = true;
            }

        }
}
</script>
<style>
.Notification,
Notification>* {
    box-sizing: border-box;
}

.Notification {
    height: 72px;
    padding: 15px 20px;
    width: 300px;
    padding-left: 70px;
    background: #fff;
    border-radius: 3px;
    position: relative;
    cursor: pointer;
    margin-bottom: 10px;
    animation: fadeInUp 1s both;
    box-shadow: 0 2px 4px 1px rgba(0, 0, 0, .1);
    border: 1px solid #ddd;
}

.Notification-wrap {
    position: fixed;
    z-index: 9;
    right: 10px;
    top: 60px;
}

.Notification-close {
    position: absolute;
    right: 10px;
    top: 10px;
    font-size: 12px;
    color: #666;
    display: block;
}

.Notification-icon {
    width: 40px;
    height: 40px;
    position: absolute;
    left: 20px;
    border-radius: 50%;
    overflow: hidden;
    top: 15px;
}

.Notification-icon>img {
    width: 100%;
}

.Notification-content>h4 {
    font-weight: bold;
    margin: 0;
    text-align: left;
}

.Notification-content>p {
    margin: 0;
    width: 90%;
    overflow: hidden;
    text-align: left;
    text-overflow: ellipsis;
    white-space: nowrap;
    line-height: 24px;
    font-size: 12px;
    color: #999;
}

@keyframes fadeInUp {
    from {
        opacity: 0;
        transform: translate3d(0, 100%, 0);
    }
    to {
        opacity: 1;
        transform: none;
    }
}
</style>
