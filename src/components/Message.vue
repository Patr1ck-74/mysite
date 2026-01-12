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

/* --- 核心美化部分：多色动态流光 --- */

.gradient-text .char {
  display: inline-block;
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
  /* 增大背景比例，让渐变色带更窄，流动感更强 */
  background-size: 200% auto;
  /* 使用 linear 确保流动不卡顿，4s 速度适中 */
  animation: gradientFlow 4s linear infinite;
}

/* 方案：三组高饱和度互补色，确保每个字视觉重心不同 */

/* 第一组：青翠电光 (Cyan/Lime) */
.gradient-text .char:nth-child(3n + 1) {
  background-image: linear-gradient(
    90deg, 
    #00f2fe 0%, #4facfe 25%, #00f2fe 50%, #4facfe 75%, #00f2fe 100%
  );
}

/* 第二组：极光幻紫 (Purple/Pink) */
.gradient-text .char:nth-child(3n + 2) {
  background-image: linear-gradient(
    90deg, 
    #7028e4 0%, #e5b2ca 25%, #7028e4 50%, #e5b2ca 75%, #7028e4 100%
  );
}

/* 第三组：落日金橙 (Orange/Gold) */
.gradient-text .char:nth-child(3n + 3) {
  background-image: linear-gradient(
    90deg, 
    #f83600 0%, #f9d423 25%, #f83600 50%, #f9d423 75%, #f83600 100%
  );
}

/* 关键动画：通过位移 background-position 实现平滑滚动 */
@keyframes gradientFlow {
  0% {
    background-position: 0% center;
  }
  100% {
    background-position: -200% center; /* 向左无限滚动 */
  }
}

/* 保持原有的渐变移动动画兼容（如果有其他地方用到） */
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
