<template>
  <!-- 基本信息 -->
  <div class="message">
    <!-- Logo -->
    <div class="logo">
      <img class="logo-img" :src="siteLogo" alt="logo" />
      <div class="name text-hidden">
        <!-- 渐变文字，每字独立span -->
        <span class="bg gradient-text">
          <span 
            v-for="(char, index) in displayDomain.split('')" 
            :key="index" 
            class="char"
            :style="{ 'animation-delay': `${index * 0.2}s` }"
          >
            {{ char }}
          </span>
        </span>
      </div>
    </div>

    <!-- 简介 -->
    <div class="description cards" @click="changeBox">
      <div class="content">
        <Icon size="16">
          <QuoteLeft />
        </Icon>
        <Transition name="fade" mode="out-in">
          <div :key="descriptionText.hello + descriptionText.text" class="text">
            <p>{{ descriptionText.hello }}</p>
            <p>{{ descriptionText.text }}</p>
          </div>
        </Transition>
        <Icon size="16">
          <QuoteRight />
        </Icon>
      </div>
    </div>
  </div>
</template>

<script setup>
import { Icon } from "@vicons/utils";
import { QuoteLeft, QuoteRight } from "@vicons/fa";
import { Error } from "@icon-park/vue-next";
import { mainStore } from "@/store";

const store = mainStore();
const displayDomain = "AiYi"; // 可自定义显示文字

// 主页站点logo
const siteLogo = import.meta.env.VITE_SITE_MAIN_LOGO;

// 站点链接
const siteUrl = computed(() => {
  const url = import.meta.env.VITE_SITE_URL;
  if (!url) return "imsyy.top".split(".");
  if (url.startsWith("http://") || url.startsWith("https://")) {
    return url.replace(/^(https?:\/\/)/, "").split(".");
  }
  return url.split(".");
});

// 简介区域文字
const descriptionText = reactive({
  hello: import.meta.env.VITE_DESC_HELLO,
  text: import.meta.env.VITE_DESC_TEXT,
});

// 切换右侧功能区
const changeBox = () => {
  if (store.getInnerWidth >= 721) {
    store.boxOpenState = !store.boxOpenState;
  } else {
    ElMessage({
      message: "当前页面宽度不足以开启盒子",
      grouping: true,
      icon: h(Error, {
        theme: "filled",
        fill: "#efefef",
      }),
    });
  }
};

// 监听状态变化
watch(
  () => store.boxOpenState,
  (value) => {
    if (value) {
      descriptionText.hello = import.meta.env.VITE_DESC_HELLO_OTHER;
      descriptionText.text = import.meta.env.VITE_DESC_TEXT_OTHER;
    } else {
      descriptionText.hello = import.meta.env.VITE_DESC_HELLO;
      descriptionText.text = import.meta.env.VITE_DESC_TEXT;
    }
  },
);
</script>

<style lang="scss" scoped>
/* 1. 必须引入字体库才能生效 */
@import url('https://fonts.googleapis.com/css2?family=Cookie&display=swap');

.message {
  .logo {
    display: flex;
    flex-direction: row;
    align-items: center;
    animation: fade 0.5s;
    max-width: 460px;

    .logo-img {
      border-radius: 50%;
      width: 120px;
    }

    .name {
      width: 100%;
      padding-left: 22px;
      transform: translateY(-8px);
      /* 2. 将字体改为 Cookie */
      font-family: "Cookie", cursive; 

      .bg {
        font-size: 7rem;
        display: flex;
        flex-wrap: wrap;
      }
    }

    @media (max-width: 768px) {
      .logo-img {
        width: 100px;
      }
      .name {
        height: 128px;
        .bg {
          font-size: 4.5rem;
        }
      }
    }

    @media (max-width: 720px) {
      max-width: 100%;
    }
  }

  .description {
    padding: 1rem;
    margin-top: 3.5rem;
    max-width: 460px;
    animation: fade 0.5s;

    .content {
      display: flex;
      justify-content: space-between;

      .text {
        margin: 0.75rem 1rem;
        line-height: 2rem;
        margin-right: auto;
        transition: opacity 0.2s;

        p {
          &:nth-of-type(1) {
            /* 这里也同步改为 Cookie 以保持风格统一，或者你可以保留 Pacifico */
            font-family: "Cookie", cursive; 
          }
        }
      }

      .xicon:nth-of-type(2) {
        align-self: flex-end;
      }
    }

    @media (max-width: 720px) {
      max-width: 100%;
      pointer-events: none;
    }
  }
}

/* --- 强化版动态渐变核心 (完全保留你原始的逻辑) --- */

.gradient-text .char {
  display: inline-block;
  background: linear-gradient(
    90deg,
    #ff0080, #ff8c00, #40e0d0, #0080ff, #b224ff, #ff0080
  );
  background-size: 400% 100%;
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
  
  animation: gradientMove 8s linear infinite;
}

.gradient-text .char:nth-child(2) { background-position: 25% 0; }
.gradient-text .char:nth-child(3) { background-position: 50% 0; }
.gradient-text .char:nth-child(4) { background-position: 75% 0; }
.gradient-text .char:nth-child(5) { background-position: 100% 0; }

@keyframes gradientMove {
  0% {
    background-position: 0% 50%;
  }
  50% {
    background-position: 100% 50%;
  }
  100% {
    background-position: 0% 50%;
  }
}

@keyframes fade {
  from {
    opacity: 0;
    transform: translateY(10px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}
</style>
