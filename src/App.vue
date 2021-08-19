<template>
    <div id="app">
        <div class="mo-yu-container">
            <div class="input-part">
                <div class="input-panel" ref="msgInputContainer"
                     placeholder="一起探讨先进的摸鱼技术！"
                     contenteditable="true" spellcheck="false">
                </div>
                <div class="action-bar" id="input-action-bar">
                    <span class="emoji-btn" @click="handleEmojiPanelVisibility">表情</span>
                    <span class="post-btn">发布</span>
                </div>
            </div>
            <div class="emoji-container" id="emoji-container" v-show="isEmojiShow">
                <div class="emoji-list">
                    <div class="emoji-title-history" v-if="historyList.length!==0">
                        最近使用
                    </div>
                    <div class="emoji-history-list" v-if="historyList.length!==0">
                    <span v-for="i in historyList" :key="i">
                      <img class="emoji-item" @click="onEmojiClick(i)"
                           :src="'https://cdn.sunofbeaches.com/emoji/'+i+'.png'">
                    </span>
                    </div>
                    <div class="emoji-title-all">
                        全部
                    </div>
                    <div class="emoji-all-list">
                     <span v-for="i in 130" :key="i">
                      <img @click="onEmojiClick(i)" class="emoji-item"
                           :src="'https://cdn.sunofbeaches.com/emoji/'+i+'.png'">
                     </span>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
    export default {
        name: 'App',
        data() {
            return {
                historyList: [],
                isEmojiShow: false
            }
        },
        methods: {
            handleEmojiPanelVisibility() {
                this.isEmojiShow = !this.isEmojiShow;
            },
            removeHistory() {
                //清空历史
            },
            //表情点击事件
            onEmojiClick(i) {
                //如果输入框没有焦点，那么让它获取焦点
                if (this.$refs.msgInputContainer !== document.activeElement) {
                    this.$refs.msgInputContainer.focus();
                }
                //往焦点出插入内容
                let targetUrl = "https://cdn.sunofbeaches.com/emoji/" + i + ".png";
                console.log("targetUrl==> " + targetUrl);
                let imageTag = `<img src="${targetUrl}" width="20" height="20">`;
                document.execCommand("insertHtml", false, imageTag);
                //保存历史记录
                //先要获取出来，然后进行拼接
                let targetStr = i.toString();
                let items = window.localStorage.getItem("emoji-history");
                if (items) {
                    //进行切割
                    let currentIndex = 0;
                    let itemArray = items.split(",");
                    for (let j = 0; j < itemArray.length; j++) {
                        //从头开始加,targetStr一定是有的了
                        //所以，先添加逗号
                        //判断i是否已经包含了
                        let item = itemArray[j];
                        if (targetStr.indexOf(item) === -1) {
                            targetStr += ",";
                            targetStr += item;
                            currentIndex++;
                        }
                        //1+1,2,3,4,5==>6个元素
                        if (currentIndex > 4) {
                            break;
                        }
                    }
                }
                //console.log("targetStr ==> " + targetStr);
                //最多保存6个，也就是最近使用
                window.localStorage.setItem("emoji-history", targetStr);
                this.updateHistory();
            },
            updateHistory() {
                let items = window.localStorage.getItem("emoji-history");
                if (items) {
                    this.historyList = items.split(",");
                }
            }
        },
        mounted() {
            //防止点击表情的时候输入框失去焦点
            let inputPart = document.getElementById("input-action-bar");
            if (inputPart) {
                inputPart.addEventListener("mousedown", function (e) {
                    e.preventDefault();
                })
            }
            let emojiContainer = document.getElementById("emoji-container");
            if (emojiContainer) {
                emojiContainer.addEventListener("mousedown", function (e) {
                    e.preventDefault();
                })
            }
            //更新历史
            this.updateHistory();
        }
    }
</script>

<style>

    html {
        background: #F4F5F6;
    }

    .emoji-btn {
        padding: 5px 0;
        color: dodgerblue;
        cursor: pointer;
    }

    .post-btn {
        cursor: pointer;
        border-radius: 4px;
        padding: 5px 20px;
        background: dodgerblue;
        color: white;
    }

    .action-bar {
        display: flex;
        justify-content: space-between;
    }


    .action-bar {
        padding: 10px 10px 0;
    }

    .input-part {
        padding: 10px;
    }

    .mo-yu-container {
        width: 420px;
        background: #fff;
        margin: 30px auto;
    }

    .emoji-item {
        width: 45px;
        cursor: pointer;
        height: 45px;
        padding: 10px;
    }

    .emoji-list {
        width: 400px;
        height: 300px;
        padding: 10px;
        overflow-y: scroll;
        overflow-x: hidden;
    }


    /*滚动条效果*/
    .emoji-list::-webkit-scrollbar {
        width: 10px;
    }

    .emoji-list::-webkit-scrollbar-track {
        background-color: #F9FAFB;
        -webkit-border-radius: 2em;
        -moz-border-radius: 2em;
        border-radius: 2em;
    }

    .emoji-list::-webkit-scrollbar-thumb {
        background-color: #E5E6EB;
        -webkit-border-radius: 2em;
        -moz-border-radius: 2em;
        border-radius: 2em;
    }

    .input-panel:focus {
        border: dodgerblue 2px solid;
    }

    .input-panel img {
        margin: 0 2px;
        vertical-align: middle;
    }

    .input-panel {
        margin: 0 auto;
        font-size: 14px;
        line-height: 20px;
        background: #efefef;
        width: 380px;
        height: 100px;
        outline: none;
        border: #F4F5F6 2px solid;
        border-radius: 4px;
        padding: 5px;
    }

    .input-panel:empty:before {
        content: attr(placeholder);
        position: absolute;
        color: #4d4d4d;
        background-color: transparent;
    }

</style>
