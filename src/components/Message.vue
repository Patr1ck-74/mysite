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
      font-family: "Pacifico-Regular";

      .bg {
        font-size: 5rem;
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
            font-family: "Pacifico-Regular";
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

/* --- 核心修改部分开始 --- */

/* 基础字符样式 */
.gradient-text .char {
  display: inline-block;
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
  background-size: 400% 400%; /* 拉大背景以实现流动效果 */
  animation: gradientMove 6s ease infinite; /* 调整动画时长，更丝滑 */
}

/* 方案：循环使用3种不同的极光/流光渐变色 
   这样相邻的字颜色差异大，整体又很和谐
*/

/* 第 1, 4, 7... 个字：赛博蓝紫 (Cool & Tech) */
.gradient-text .char:nth-child(3n + 1) {
  background-image: linear-gradient(
    135deg,
    #00c6fb 0%,   /* 亮青 */
    #005bea 50%,  /* 深蓝 */
    #00c6fb 100%  /* 回归亮青以实现无缝循环 */
  );
}

/* 第 2, 5, 8... 个字：日落熔岩 (Warm & Passion) */
.gradient-text .char:nth-child(3n + 2) {
  background-image: linear-gradient(
    135deg,
    #f83600 0%,   /* 橙红 */
    #f9d423 50%,  /* 金黄 */
    #f83600 100%
  );
}

/* 第 3, 6, 9... 个字：霓虹幻紫 (Dreamy & Magic) */
.gradient-text .char:nth-child(3n + 3) {
  background-image: linear-gradient(
    135deg,
    #b721ff 0%,   /* 紫罗兰 */
    #21d4fd 50%,  /* 电光蓝 */
    #b721ff 100%
  );
}

/* 渐变移动动画 */
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
/* --- 核心修改部分结束 --- */
</style>
