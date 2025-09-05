<template>
  <footer id="footer" :class="store.footerBlur ? 'blur' : null">
    <Transition name="fade" mode="out-in">
      <div v-if="!store.playerState || !store.playerLrcShow" class="power">
        <span>
          <span :class="startYear < fullYear ? 'c-hidden' : 'hidden'">Copyright&nbsp;</span>
          &copy;
          <span v-if="startYear < fullYear" class="site-start">
            {{ startYear }} -
          </span>
          {{ fullYear }}
          <!-- 这里直接写你自定义的文字 -->
          <span class="custom-author">Patrick Shaw</span>
        </span>

        <!-- 以下信息请不要修改 -->
        <span class="hidden">
          &amp;&nbsp;Made&nbsp;by
          <a :href="config.github" target="_blank">{{ config.author }}</a>
        </span>

        <!-- 站点备案 -->
        <span>
          &amp;
          <a v-if="siteIcp" href="https://beian.miit.gov.cn" target="_blank">
            {{ siteIcp }}
          </a>
        </span>
      </div>

      <div v-else class="lrc">
        <Transition name="fade" mode="out-in">
          <div class="lrc-all" :key="store.getPlayerLrc">
            <music-one theme="filled" size="18" fill="#efefef" />
            <span class="lrc-text text-hidden" v-html="store.getPlayerLrc" />
            <music-one theme="filled" size="18" fill="#efefef" />
          </div>
        </Transition>
      </div>
    </Transition>
  </footer>
</template>

<script setup>
import { ref } from "vue";
import { MusicOne } from "@icon-park/vue-next";
import { mainStore } from "@/store";
import config from "@/../package.json";

const store = mainStore();
const fullYear = new Date().getFullYear();
const startYear = ref(import.meta.env.VITE_SITE_START?.substring(0, 4) || null);
const siteIcp = ref(import.meta.env.VITE_SITE_ICP);
</script>

<style scoped lang="scss">
#footer {
  width: 100%;
  position: absolute;
  bottom: 0;
  left: 0;
  height: 46px;
  line-height: 46px;
  text-align: center;
  font-size: 14px;
  white-space: nowrap;

  .custom-author {
    margin-left: 4px;
    font-weight: bold;
  }

  .lrc {
    display: flex;
    justify-content: center;
    align-items: center;
  }

  &.blur {
    backdrop-filter: blur(10px);
    background: rgba(0,0,0,0.25);
  }
}
</style>
