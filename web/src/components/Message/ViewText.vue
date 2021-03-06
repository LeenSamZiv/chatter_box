<template>
    <div class="text-message"
         :style="{flexDirection:item.self?'row-reverse':'row'}">
        <div class="avatar" :class="{'flip-horizontal':!item.self}">{{item.avatar}}</div>
        <div class="divider"></div>
        <div class="content">
            <div class="host-address" :style="{textAlign:item.self?'right':'left'}">
                <span @click="readContent()">{{item.hostAddress}}</span>
            </div>
            <div class="message-stand" :style="{flexDirection:item.self?'row-reverse':'row'}">
                <div v-if="item.flag === messageFlag.MESSAGE" class="text"
                     :class="{'self-text':item.self}"
                     v-html="textFormat(item.content)">
                </div>
                <div v-if="item.flag === messageFlag.BINARY_PIC">
                    <img preview="0" v-if="item.loadDone" :src="item.binary"
                         @load="handleImageLoad()">
                    <div v-else class="binary-loading"></div>
                </div>
            </div>
        </div>
        <div class="divider"></div>
        <div class="avatar fake-avatar"></div>
    </div>
</template>

<script>
    import MessageFlag from "../../util/MessageFlag";
    import {HttpRegExp} from "../../configs/consts";
    import {readText} from "../../util/Text2Speech";

    export default {
        name: "ViewText",
        props: {
            item: Object
        },
        computed: {
            messageFlag: function () {
                return MessageFlag;
            },
            textFormat: function () {
                return s => {
                    s = s.replace(
                        HttpRegExp,
                        i => `<a href="${i}" target="_blank">${i}</a>`
                    );
                    return s;
                };
            },
        },
        methods: {
            readContent() {
                if (this.item.flag === MessageFlag.MESSAGE) {
                    readText(this.item.content);
                } else if (this.item.flag === MessageFlag.BINARY_PIC) {
                    readText("一张图片");
                }
            },
            handleImageLoad() { // 图片加载结束
                this.$previewRefresh(); // 图片预览刷新
            }
        },
    }
</script>

<style lang="scss" scoped>
    .text-message {
        display: flex;

        > .avatar {
            background: $color-yellow;
            width: $spacing-normal*8;
            height: $spacing-normal*8;
            border-radius: 50%;
            display: flex;
            justify-content: center;
            align-items: center;
            @extend .copy-unable;
        }

        > .divider {
            width: $spacing-normal;
        }

        > .content {
            color: $color-white;
            flex: 1;
            display: flex;
            flex-direction: column;

            > .host-address {
                font-size: 0.8em;

                > span {
                    cursor: pointer;
                }
            }

            > .message-stand {
                flex: 1;
                display: flex;
                align-items: center;
                justify-content: flex-start;

                > .text {
                    word-break: break-all;
                    border-radius: 2px;
                    background: $color-gray;
                    padding: $spacing-normal*1.5;
                    font-weight: normal;

                    &.self-text {
                        color: $color-black;
                        background: $color-yellow;
                    }
                }

                img {
                    min-height: $spacing-normal*4;
                    max-height: 300px;
                    max-width: 100%;
                    object-fit: cover;
                    object-position: left top;
                    border-radius: 4px;
                    box-sizing: border-box;
                }

                .binary-loading {
                    margin: $spacing-normal;
                    border: 3px solid $color-gray;
                    border-top-color: $color-yellow;
                    border-radius: 50%;
                    width: 3em;
                    height: 3em;
                    animation: spin 1.5s linear infinite;
                }

                @keyframes spin {
                    to {
                        transform: rotate(360deg);
                    }
                }
            }
        }

        > .fake-avatar {
            opacity: 0;
        }
    }
</style>
