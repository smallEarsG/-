<template>
  <div class="chat-container">
    <div ref="screenshotRef">
      <div class="chat-header">
        <el-icon class="back-button">
          <ArrowLeft />
        </el-icon>
        <span class="chat-title">{{ form.nickname || '微信聊天' }}</span>
        <el-icon class="menu-button">
          <MoreFilled />
        </el-icon>
      </div>
      <div class="chat-window">
        <div v-for="(line, index) in messageLines" :key="index"
          :class="{ 'mine': index % 2 === 1, 'theirs': index % 2 === 0 }">

          <div v-if="true" class="time">{{ form.theirsName || '是否显示当前时间' }}</div>
          <div class="chat-message">
            <div>
              <div v-if="index % 2 === 0" class="nickname">{{ form.theirsName || '是否显示当前时间' }}</div>
              <el-avatar class="avatar" shape="square"
                :src="index % 2 === 1 ? mineAvatar || defaultAvatar : theirsAvatar || defaultAvatar" />
            </div>

            <div class="message-wrapper">
              <div class="message-bubble " :class="{ 'right': index % 2 === 1, 'left': index % 2 === 0 }">
                {{ line }}
              </div>
            </div>
          </div>
        </div>
      </div>
      <WxInputBar />
    </div>
    <div class="chat-input">
      <el-input v-model="form.nickname" placeholder="聊天标题（群名或对方昵称）" class="input" />
      <el-input v-model="mineAvatar" placeholder="我的头像 URL" class="input" />
      <el-input v-model="theirsAvatar" placeholder="对方的头像 URL" class="input" />
      <el-input v-model="form.theirsName" placeholder="对方昵称" class="input" />
      <el-input type="textarea" v-model="form.message" placeholder="请输入聊天内容（每行一个消息）" class="input" />
      <el-button type="primary" @click="generateImage" class="send-button">✨ 生成截图</el-button>
      <div></div>
      <el-button type="success" @click="downloadImage" class="send-button">📥 下载图片</el-button>


    </div>
    <div v-if="generatedImage" class="screenshot-result">
      <h2>🎉 生成结果：</h2>
      <el-image :src="generatedImage" class="screenshot" fit="cover" />
    </div>
  </div>
</template>

<script setup>
import { ref, computed } from 'vue'
import html2canvas from 'html2canvas'
import { ArrowLeft, MoreFilled } from '@element-plus/icons-vue'
import WxInputBar from './components/WxInputBar.vue'
const form = ref({
  nickname: '',
  message: '',
  theirsName: ''
})
const mineAvatar = ref('')
const theirsAvatar = ref('')
const generatedImage = ref('')
const screenshotRef = ref(null)
const defaultAvatar = 'https://img.yzcdn.cn/vant/cat.jpeg' // 默认头像

const messageLines = computed(() => form.value.message.split('\n').filter(Boolean))

const generateImage = async () => {
  if (!screenshotRef.value) {
    console.error("截图失败：screenshotRef 为空！");
    return;
  }
  try {
    const canvas = await html2canvas(screenshotRef.value, { useCORS: true, backgroundColor: null });
    generatedImage.value = canvas.toDataURL("image/png");
  } catch (error) {
    console.error("截图出错：", error);
  }
}

const downloadImage = () => {
  if (!generatedImage.value) return;
  const link = document.createElement('a');
  link.href = generatedImage.value;
  link.download = 'wechat-chat.png';
  document.body.appendChild(link);
  link.click();
  document.body.removeChild(link);
}
</script>

<style scoped>
.chat-container {
  width: 375px;
  background: #e5e5e5;
  border-radius: 10px;
  padding: 10px;
  margin: auto;
  font-family: Arial, sans-serif;
}

.screenshotRef {
  width: 1334px;
}

.chat-header {
  display: flex;
  align-items: center;
  justify-content: space-between;
  background: #ffffff;
  padding: 10px;

  font-weight: bold;
}

.back-button,
.menu-button {
  font-size: 18px;
  cursor: pointer;
}

.chat-title {
  flex-grow: 1;
  text-align: center;
}

.chat-window {
  min-height: 450px;
  background: #f5f5f5;
  padding: 10px;
  display: flex;
  flex-direction: column;
  /* border-radius: 10px; */
}

.chat-message {
  display: flex;

  margin-bottom: 10px;
  width: 100%;
}

.avatar {
  width: 42px;
  height: 42px;
  border-radius: 5px;
}

.message-wrapper {
  max-width: 75%;
  display: flex;
  flex-direction: column;
}

.right::after {
  content: "";
  position: absolute;
  top: 13px;
  /* 三角形的位置 */
  right: -6px;
  transform: translateX(50%);
  border-width: 7px 8px 7px;
  border-style: solid;
  border-color: transparent transparent transparent rgb(125, 226, 79);
  /* 上面是消息框的背景色 */
}

.left::after {
  content: "";
  position: absolute;
  top: 13px;
  /* 三角形的位置 */
  left: -6px;
  transform: translateX(-50%);
  border-width: 7px 8px 7px;
  border-style: solid;
  border-color: transparent rgb(255, 255, 255) transparent transparent;
  /* 上面是消息框的背景色 */
}


.nickname {
  font-size: 12px;
  color: gray;
  margin-bottom: 2px;
  text-align: center;
}

.message-bubble {
  padding: 10px 14px;
  border-radius: 10px;
  font-size: 16px;
  position: relative;
}

.bubble-tail {
  position: absolute;
  bottom: 6px;
  width: 12px;
  height: 12px;
}

.mine-tail {
  right: -10px;
}

.theirs-tail {
  left: -10px;
}

.mine {
  flex-direction: row-reverse;
  text-align: right;
}

.mine .message-bubble {
  background: rgb(125, 226, 79);
  color: black;
  border-radius: 10px;
  margin-right: 10px;
}

.theirs {
  text-align: left;
}

.theirs .message-bubble {
  background: #fff;
  /* border: 1px solid #ddd; */
  border-radius: 10px;
  margin-left: 13px;
}

.chat-input {
  display: flex;
  flex-direction: column;
  gap: 5px;
  margin-top: 10px;
}

.input {
  /* padding: 8px; */
  border: 1px solid #ccc;
  border-radius: 5px;
}

.send-button {
  width: 100%;
  margin-top: 5px;
}

.screenshot-result {
  margin-top: 10px;
  text-align: center;
}

.screenshot {
  max-width: 100%;
  border-radius: 10px;
  border: 1px solid #ddd;
}
</style>