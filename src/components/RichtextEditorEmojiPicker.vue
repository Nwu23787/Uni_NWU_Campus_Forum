<!-- 富文本表情选择器 -->
<template>
    <van-popup v-model:show="show_popup" class="richtextEditorEmojiPicker" :lock-scroll="false" position="bottom" :close-on-popstate="true" close-icon="close" :round="true" :closeable="true"  @closed="closed()">
        
        <van-loading v-if="show_loading" type="spinner" color="#1989fa" style="height: 100%;  display: flex;flex-direction: column;justify-content: center;align-items: center;"/>

        <div class="emoticon-container">
            <van-tabs v-model:active="state.type" swipeable >
                <van-tab title="图片表情" :name="'image'">
                    <div class="emoticon-tab-content">
                        <div class="scroll">
                            <div class="emoticon-tab-item-container">
                                <span v-for="image in state.imageList" class="emoticon-tab-image-item" @click="onEmojiSelect(image,'image')">
                                    <img :src="image"/>
                                </span>
                            </div>
                        </div>
                    </div>
                </van-tab>
                <van-tab title="Emoji" :name="'emoji'">
                    <div class="emoticon-tab-content">
                        <div class="scroll">
                            <div class="emoticon-tab-item-container">
                                <span v-for="emoji in state.emojiList" class="emoticon-tab-emoji-item" @click="onEmojiSelect(emoji,'emoji')">
                                    <span class="emoji">{{emoji}}</span>
                                </span>
                            </div>
                        </div>
                    </div>
                </van-tab>
            </van-tabs>
        </div>
    </van-popup>
</template>

<script setup lang="ts">
    import { nextTick, onMounted, reactive, ref} from 'vue'
    import { definePropType } from '@/utils/tool';



    const show_popup = ref(false);

    const show_loading = ref(false);

    const state = reactive({
        type :'image',//类型 image、emoji
        imageList:[] as Array<string>,//图片表情
        emojiList:[] as Array<string>,//Emoji表情
    });
   
    //接收父组件消息
    const props = defineProps({
        show: {
            type: Boolean,
            default: false
        },
        emojiPath: {
            type: String,
            default: false
        },
        onEmojiSelect: {//表情符号选择
            type: Function,
            default: null
        },
        close: {
            type: definePropType<() => void>(Function),
            required: true,
        }
      //  onClickOutside: {//在选取器外部发生单击时回调
      //      type: Function,
      //      default: null
      //  },
    })  
    

    const handleCreateEmojiPicker = () => {
        show_popup.value = props.show;
        
        nextTick(()=>{
            show_loading.value = true;
                   
                    
            for(let i=0; i<152; i++){
                state.imageList.push(props.emojiPath+'pc/js/kindeditor/plugins/emoticons/twemoji/'+i+'.svg');
            }
            

            let emojis:any = {
                people: ['😀','😃','😄','😁','😆','😅','🤣','😂','🙂','🙃','😉','😊','😇','🥰','😍','🤩','😘','😗','😚','😙','😋','😛','😜','🤪','😝','🤑','🤗','🤭','🤫','🤔','🤐','🤨','😐','😑','😶','😏','😒','🙄','😬','🤥','😌','😔','😪','🤤','😴','😷','🤒','🤕','🤢','🤮','🤧','🥵','🥶','🥴','😵','🤯','🤠','🥳','😎','🤓','🧐','😕','😟','🙁','☹️','😮','😯','😲','😳','🥺','😦','😧','😨','😰','😥','😢','😭','😱','😖','😣','😞','😓','😩','😫','🥱','😤','😡','😠','🤬','😈','👿','💀','☠️','💩','🤡','👹','👺','👻','👽','👾','🤖','👋','🤚','🖐️','✋','🖖','👌','🤏','✌️','🤞','🤟','🤘','🤙','👈','👉','👆','🖕','👇','☝️','👍','👎','✊','👊','🤛','🤜','👏','🙌','👐','🤲','🤝','🙏','✍️','💅','🤳','💪','🦾','🦿','🦵','🦶','👂','🦻','👃','🧠','🦷','🦴','👀','👁️','👅','👄','😺','😸','😹','😻','😼','😽','🙀','😿','😾','🙈','🙉','🙊','💋','💌','💘','💝','💖','💗','💓','💞','💕','💟','❣️','💔','❤️','💯','💢','💥','💫','💦','💨','🕳️','💣','💬','👁️‍🗨️','🗨️','🗯️','💭','💤'],//表情与角色
                nature: ['🐵','🐒','🦍','🦧','🐶','🐕','🦮','🐕‍🦺','🐩','🐺','🦊','🦝','🐱','🐈','🦁','🐯','🐅','🐆','🐴','🐎','🦄','🦓','🦌','🐮','🐂','🐃','🐄','🐷','🐖','🐗','🐽','🐏','🐑','🐐','🐪','🐫','🦙','🦒','🐘','🦏','🦛','🐭','🐁','🐀','🐹','🐔','🐓','🦢','🐍','🐲','🐉','🦕','🦖','🐳','🐋','🐬','🐟','🐠','🐡','🦈','🐙','🐚','🐌','🦋','🐛','🐜','🐝','🐞','🦗','🕷️','🕸️','🦂','🦟','🦠','💐','🌹','🥀','🌲','🌳','🌴','🌵','🍁'],//动物与自然
                foods: ['🍇','🍈','🍉','🍊','🍋','🍌','🍍','🥭','🍎','🍏','🍐','🍑','🍒','🍓','🥝','🍅','🥥','🥑','🍆','🥔','🥕','🌽','🌶️','🥒','🥬','🥦','🧄','🧅','🍄','🍖','🍗','🦀','🦞','🦐','🦑','🥧','🍫','🍬','🍭','🍮','🍯','🍼','🥛','☕'],//食物与饮品
                activity: ['🎃','🎄','🎆','🧨','🎋','🧧','🎀','🎁','🎗️','🎟️','🎫','🎖️','🏆','🏅','⚽','⚾','🥎','🏀','🃏','🀄','🎴'],//活动
                places: ['🏝️','🏞️','🏟️','🏛️','🏗️','🧱','🏡','🏢','🗼','🗽','🚑','🚒','🚓','🚔','🚕','🚖','🚗','🚢','✈️','🛰️','🚀','🛸','☀️','🌝','🌞','🪐','⭐','🌟','🌠','🌂','⚡','❄️','🔥','💧'],//旅行与景点
                objects: ['👓','🕶️','🥽','🥼','🦺','👔','👕','👖','🧣','🧤','🧥','🧦','👗','👘','🥻','🩱','🩲','🩳','👙','👚','👛','👜','👝','🛍️','🎒','👞','👟','🥾','🥿','👠','👡','🩰','👢','👑','👒','🎩','🎓','🧢','⛑️','📿','💄','💍','💎','🔇','🔈','🔉','🔊','📢','📣','📯','🔔','🔕','🎼','🎵','🎶','🎙️','🎚️','🎛️','🎤','🎧','📻','🎷','🎸','🎹','🎺','🎻','🪕','🥁','📱','📲','☎️','📞','📟','📠','🔋','🔌','💻','🖥️','🖨️','⌨️','🖱️','🖲️','💽','💾','💿','📀','🧮','🎥','🎞️','📽️','🎬','📺','📷','📸','📹','📼','🔍','🔎','🕯️','💡','🔦','🏮','🪔','📔','📕','📖','📗','📘','📙','📚','📓','📒','📃','📜','📄','📰','🗞️','📑','🔖','🏷️','💰','💴','💵','💶','💷','💸','💳','🧾','💹','✉️','📧','📨','📩','📤','📥','📦','📫','📪','📬','📭','📮','🗳️','✏️','✒️','🖋️','🖊️','🖌️','🖍️','📝','💼','📁','📂','🗂️','📅','📆','🗒️','🗓️','📇','📈','📉','📊','📋','📌','📍','📎','🖇️','📏','📐','✂️','🗃️','🗄️','🗑️','🔒','🔓','🔏','🔐','🔑','🗝️','🔨','🪓','⛏️','⚒️','🛠️','🗡️','⚔️','🔫','🏹','🛡️','🔧','🔩','⚙️','🗜️','⚖️','🦯','🔗','⛓️','🧰','🧲','⚗️','🧪','🧫','🧬','🔬','🔭','📡','💉','🩸','💊','🩹','🩺','🚪','🛏️','🛋️','🪑','🚽','🚿','🛁','🪒','🧴','🧷','🧹','🧺','🧻','🧼','🧽','🧯','🛒','🚬','⚰️','⚱️','🗿'],//物品
                symbols: ['🏧','🚹','🚺','🚻','🚾','⚠️','🚸','⛔','🚫','🚳','🚭','🚯','🚱','🚷','📵','🔞','☢️','☣️','🔅','🔆','📶','📳','📴','✖️','➕','➖','➗','♾️','‼️','⁉️','❓','❔','❕','❗','〰️','💱','💲','⚕️','♻️','⚜️','⭕','✅','✔️','❌','#️⃣','*️⃣','0️⃣','1️⃣','2️⃣','3️⃣','4️⃣','5️⃣','6️⃣','7️⃣','8️⃣','9️⃣','🔟','🆗','🅿️','🆘','🆙','🆚','🈁','🈂️','🈷️','🈶','🈯','🉐','🈹','🈚','🈲','🉑','🈸','🈴','🈳','㊗️','㊙️','🈺','🈵'],//符号
                flags: ['🏁','🎌','🚩','🏴','🏳️']//旗帜 
            }

            for (let key in emojis) {
                state.emojiList.push(...emojis[key])
            }
            show_loading.value = false
            
        })

       


    };


    //表情符号选择
    const onEmojiSelect = (emoji: string,type:string ) => {
        //editor.txt.insertEmoticon("common/default/pc/js/kindeditor/plugins/emoticons/twemoji/9.svg", "image");
		//editor.txt.insertEmoticon("😄", "emoji");
        props.onEmojiSelect(emoji,type);
        show_popup.value = false;
    };


    //关闭弹出层且动画结束后触发。
    const closed = () => {
        props.close();
    }

    onMounted(() => {
        handleCreateEmojiPicker();
       
    }) 
   
</script>

<style scoped lang="scss">
.emoticon-container{
    width: 100%;
    height: 100%; 
    :deep(.van-tabs--line .van-tabs__wrap) {
        margin-left: var(--van-cell-group-inset-padding);
        margin-right: var(--van-cell-group-inset-padding);
        padding-bottom: 10px;
        background: #fff;
        border-radius: var(--van-cell-group-inset-border-radius) var(--van-cell-group-inset-border-radius) 0px 0px;
    }
    :deep(.van-cell-group--inset) {
        margin: 0px var(--van-cell-group-inset-padding);
        border-radius: 0px 0px var(--van-cell-group-inset-border-radius) var(--van-cell-group-inset-border-radius);
    }
    :deep(.van-tabs__line) {
        background: var(--van-blue);
    }
    .emoticon-tab-content{
        height: calc(50vh - 40px);
        .scroll{
            overflow-y:auto;
            -webkit-overflow-scrolling: touch;
            height: 100%; 
            .emoticon-tab-item-container{
                margin-top: 5px;
                margin-left: 5px;
                margin-right: 5px;
                margin-bottom: 5px;
                //display: flex;
                //justify-content: center; /* 水平居中 */
                //flex-wrap: wrap;
                .emoticon-tab-image-item {
                    cursor: pointer;
                    padding: 5px 10px;
                    display: inline-block;
                    img{
                        width: 32px;
                        height: 32px;
                    }
                }
            
                .emoticon-tab-emoji-item {
                    cursor: pointer;
                    
                    display: inline-block;
                    .emoji{
                        font-size: 30px;
                        width: 52px;
                        height: 52px;
                        display: inline-block;
                        text-align: center;
                    }
                }
                
            }
        }
        
    }
}
</style>
