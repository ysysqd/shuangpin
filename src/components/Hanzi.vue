<script setup lang="ts">
import { effect, ref } from "vue";
import { useStore } from "../store";
import { getPinyinOf } from "../utils/hanzi";
import { randomChoice } from "../utils/number";

const props = defineProps<{
  hanziSeq: string[];
}>();

const pinyin = ref("");
const currentHanzi = ref();
const settings = useStore().settings;

effect(() => {
  // eslint-disable-next-line vue/no-mutating-props
  currentHanzi.value = props.hanziSeq.pop();
  if (settings.enablePinyinHint) {
    pinyin.value = getPinyinOf(currentHanzi.value).at(0) ?? "";
  } else {
    pinyin.value = ""
  }
});
</script>

<template>
  <div class="displayer">
    <div
      v-for="(item, i) in hanziSeq"
      :key="i"
      class="follow-item"
      :style="`opacity: ${i / 4};transform: translateX(-${
        (hanziSeq.length - i + 1) * 120
      }%);`"
    >
      {{ item }}
    </div>
    <div class="current-outset">
      <div class="current-item">
        <img class="mi-bg" src="../assets/mi-bg.svg" />
        <div class="pinyin">
          {{ pinyin }}
        </div>
        <div :key="currentHanzi" class="hanzi">
          {{ currentHanzi }}
        </div>
      </div>
    </div>
  </div>
</template>

<style lang="less" scoped>
@keyframes shift-right {
  from {
    transform: translateX(0);
  }

  to {
    transform: translateX(100%);
  }
}

.displayer {
  display: flex;
  align-items: center;
  justify-content: center;
  position: relative;

  .current-outset {
    border: 1px solid var(--black);
    padding: 5px;
  }

  .follow-item {
    font-size: 36px;
    font-weight: bold;
    margin-right: 10px;
    margin-top: -1px;
    position: absolute;
    right: 0;
    transition: all ease 0.3s;
  }

  .current-item {
    @size: 54px;
    height: @size;
    width: @size;

    display: flex;
    flex-direction: column;
    align-items: center;
    position: relative;

    padding: 10px;
    border-radius: 0px;

    .mi-bg {
      position: absolute;
      height: 100%;
      width: 100%;
      top: 0;
      opacity: 0.2;
    }

    .pinyin {
      font-size: 14px;
      position: absolute;
      top: -1px;
    }

    .hanzi {
      font-size: 36px;
      font-weight: bold;
    }
  }
}
</style>
