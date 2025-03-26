<template>
    <div class="wx-input-bar">
      <div class="icon left"><img src="../assets/voice.png" alt=""></div>
      <input class="input" type="text" placeholder="" />
      <div class="icon right"><img src="../assets/smiling.png" alt=""></div>
      <div class="icon right circle "><img src="../assets/circlePlus.png"/></div>
    </div>
  </template>
  
  <script setup>
// import { Microphone, Emoji, CirclePlus } from '@element-plus/icons-vue'
  </script>
  
  <style scoped>
  .wx-input-bar {
    display: flex;
    align-items: center;
    background-color: #f8f8f8;
    padding: 6px 8px;
    border-top: 1px solid #ddd;
    width: 100%;
    box-sizing: border-box;
  }
  
  .input {
    flex: 1;
    height: 36px;
    border: none;
    border-radius: 10px;
    padding: 0 10px;
    margin: 0 6px;
    font-size: 14px;
    background-color: white;
    outline: none;
  }
  
  .icon {
    width: 36px;
    height: 36px;
    /* border-radius: 50%; */
    /* border: 1px solid #ccc; */
    display: flex;
    align-items: center;
    justify-content: center;
    overflow: hidden;
  }
  .icon img {
    width: 100%;
    /* height: 100%; */
    object-fit: cover; 
  }
  .circle{
    object-fit: cover; 
  }
  
  .icon.right {
    margin-left: 4px;
  }
  
  .icon.left {
    margin-right: 4px;
  }
  </style>
  