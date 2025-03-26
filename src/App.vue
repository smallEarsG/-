<template>
  <div class="chat-container">
    <!-- 输入表单区域 -->
    <div class="form-section">
      <input v-model="form.theirsName" placeholder="聊天名称" />
      <!-- <input v-model="form.nickname" placeholder="聊天名称" /> -->
      <!-- <input type="file" @change="uploadAvatar($event, 'mine')" /> -->
      <input type="file" @change="uploadAvatar($event, 'mine')" />
      <input type="file" @change="uploadAvatar($event, 'theirs')" />
      <textarea v-model="form.message" placeholder="输入聊天内容（每行格式：昵称：内容）"></textarea>
      <!-- 插入时间 -->
      <input v-model="currentTime" placeholder="请输入标签：如 18:58" />
      <button @click="addTag">✨ 插入标签</button>
      <button @click="generateImage">✨ 生成截图</button>
      <button @click="downloadImage">📥 下载图片</button>
      <div class="donate-box">
        <p>💖 喜欢这个工具？请我喝杯奶茶吧 ☕</p>
        <img class="donate-qr" src="./assets/wxCode.jpg" alt="赞赏二维码" />
      </div>
    </div>

    <!-- 聊天展示区域 -->
    <div ref="chatBox" class="chat-window">
      <!-- 顶部导航栏 -->
      <div class="chat-header">
        <div class="back"><el-icon>
            <ArrowLeft />
          </el-icon></div>
        <div class="chat-title">{{ form.theirsName || '微信聊天' }}</div>
        <div class="menu"><img src="./assets/more.png" /></div>
      </div>
      <div class="massageBox">
        <div v-for="(line, index) in parsedMessages" :key="index">
          <!-- 时间 -->

          <div class="time" v-if="line.from === 'tag'"> {{ line.text }}</div>
          <!-- 我的消息 -->
          <div v-else class="chat-line" :class="line.from">
            <img class="avatar" :class="line.theirsName ? 'avatarLeft' : ''"
              :src="line.from === 'mine' ? mineAvatar : theirsAvatar" />
            <div class="bubble-wrapper">
              <div class="nickname"> {{ line.theirsName }}</div>
              <div class="bubble">
                {{ line.text || '暂无内容' }}
              </div>
            </div>
          </div>
        </div>
        <WxInputBar class="inputBar" />
      </div>
    </div>








    <!-- 截图展示区 -->
    <div>
      <h3>生成截图：</h3>
    </div>
    <div v-if="generatedImage">

      <img :src="generatedImage" class="preview" />
    </div>
  </div>
</template>

<script setup>
import { ref, computed } from 'vue';
import html2canvas from 'html2canvas';
import WxInputBar from './components/WxInputBar.vue';

const form = ref({
  nickname: '',
  theirsName: '',
  message: "tag：18:59 \n" +
    "小红：这是测试数据 \n" +
    "我：这是第二条测试数据\n" +
    "tag：“小红撤回了一条消息”"
});
const messagesList = ref([
  {
    from: 'mine',
    text: '你好，我是小明'
  },
  {
    from: 'theirs',
    theirsName: '小红',
    text: '你好，我是小红 你好，我是小红你好，我是小红你好，我是小红'
  },
  {
    from: 'tag',
    text: '18:47'
  },
  {
    from: 'mine',
    text: '你好，我是小明'
  }
]);
const currentTime = ref('');
const mineAvatar = ref('https://img.yzcdn.cn/vant/cat.jpeg');
const theirsAvatar = ref('https://img.yzcdn.cn/vant/cat.jpeg');
const chatBox = ref(null);
const generatedImage = ref('');


const parsedMessages = computed(() => {
  return form.value.message.split('\n').map(line => {
    const [prefix, ...rest] = line.split('：');
    const text = rest.join('：');
    if (prefix === 'tag') return { from: 'tag', text }; // tag
    if (prefix === '我') return { from: 'mine', text };
    return { from: 'theirs', theirsName: prefix, text: text }; // fallback
  });
});
function addTag() {
  form.value.message += `\ntag：${currentTime.value}`;

}
function uploadAvatar(e, role) {
  const file = e.target.files[0];
  if (!file) return;
  const reader = new FileReader();
  reader.onload = () => {
    if (role === 'mine') mineAvatar.value = reader.result;
    else theirsAvatar.value = reader.result;
  };
  reader.readAsDataURL(file);
}

async function generateImage() {

  const canvas = await html2canvas(chatBox.value, {
    scale: 2 // ✅ 默认是1，我们设为2或3
  });
  generatedImage.value = canvas.toDataURL();
}

function downloadImage() {
  const a = document.createElement('a');
  a.href = generatedImage.value;
  a.download = 'chat.png';
  a.click();
}
</script>

<style scoped>
.chat-container {
  /* width: 375px;
  margin: auto; */
  display: flex;
  font-family: '思源黑体';
  /* font-family: -apple-system, BlinkMacSystemFont, '思源黑体', Roboto; */
  /* background: #cac9c9; */
  background-color: #cac9c9;
  padding: 10px;
}

.chat-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  background: #ededed;
  padding: 10px 14px;

  border-radius: 6px;
  border-bottom: 1PX solid #ddd;

}

.chat-header .back {
  display: flex;
  align-items: center;

}

.chat-header .menu {
  display: flex;
  align-items: center;

}


.chat-title {
  font-weight: 600;
  flex: 1;
  text-align: center;

}

.menu img {
  width: 25px;
  height: auto;
}


.chat-window {
  /* background: #f1f1f1; */
  /* padding: 10px 12px; */
  position: relative;
  min-height: 300px;
  margin-top: 10px;
  border-radius: 6px;
  box-shadow: 0 1px 3px rgba(0, 0, 0, 0.1);
  display: flex;
  flex-direction: column;
  justify-content: flex-start;
  width: 375px;
  /* overflow-y: auto */
  margin: 0 20px;
  height: 575px;
  background-color: #ededed !important;
  color: #000;
}

.massageBox {
  padding-top: 10px;
  background: #f1f1f1;
  height: 100%;

}

.chat-line {
  display: flex;
  align-items: flex-start;
  margin-bottom: 14px;
  line-height: 1.4;
  height: 100%;
}

.chat-line.mine {
  flex-direction: row-reverse;
}

.avatar {
  width: 42px;
  height: 42px;
  border-radius: 6px;
  margin: 0px 10px;
  object-fit: cover;
}

.avatarLeft {
  margin-top: 2px;
}

.bubble-wrapper {
  max-width: 65%;
  position: relative;
}

.nickname {
  font-size: 12px;
  color: #999;
  margin-bottom: 4px;
  text-align: left;
}

.time {
  font-size: 13px;
  color: #999;
  margin-bottom: 8px;
  /* letter-spacing: 1px; */
  /* word-spacing: 8px; */
}

.bubble {
  padding: 8px 12px;
  font-size: 15px;
  border-radius: 6px;
  position: relative;
  word-break: break-word;
  line-height: 1.5;
}

.chat-line.mine .bubble {
  background: #9cda62;
  /* color: #fff; */
}

.chat-line.theirs .bubble {
  background: #fff;
  /* border: 1px solid #ddd; */
}

.chat-line.mine .bubble::after {
  content: "";
  position: absolute;
  top: 10px;
  right: -6px;
  width: 0;
  height: 0;
  border-top: 6px solid transparent;
  border-bottom: 6px solid transparent;
  border-left: 6px solid #9cda62;
}

.chat-line.theirs .bubble::after {
  content: "";
  position: absolute;
  top: 10px;
  left: -6px;
  width: 0;
  height: 0;
  border-top: 6px solid transparent;
  border-bottom: 6px solid transparent;
  border-right: 6px solid white;
}

.form-section {
  display: flex;
  flex-direction: column;
  gap: 6px;
  margin-top: 10px;
}

textarea {
  height: 100px;
  resize: none;
  padding: 6px;
  font-size: 14px;
}

button {
  padding: 8px;
  border: none;
  border-radius: 4px;
  background-color: #409eff;
  color: white;
  font-size: 14px;
  cursor: pointer;
}

button+button {
  margin-top: 4px;
  background-color: #67c23a;
}

.preview {
  max-width: 100%;
  border: 1px solid #ccc;
  border-radius: 6px;
  /* margin-top: 10px; */
}

.inputBar {
  position: absolute;
  bottom: 0;
  left: 0;
  right: 0;
}
.donate-box {
  font-size: 14px;
  color: #888;
  margin-top: 20px;
}
.donate-qr {
  width: 200px;
  height: 180px;
  border-radius: 6px;
}
</style>
