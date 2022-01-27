<template>
  <n-config-provider :hljs="hljs">
    <n-layout has-sider class="h-full" sider-placement="right">
      <n-layout class="flex h-full flex-col">
        <n-layout-header bordered class="h-[80px] *flex-center bg-[#eee]">
          <div class="logo uppercase">box shadow vis</div>
        </n-layout-header>
        <n-layout-content class="content *flex-center" content-style="flex:1">
          <div class="playground">
            <div class="draw-content flex-1 *flex-center">
              <div id="ceil" :style="[{ boxShadow }, myStyle]"></div>
            </div>
            <div class="flex h-[200px] w-full gap-4 px-4">
              <div class="show flex-1">
                <n-code :code="myStyle" language="css" />
              </div>
              <div class="input flex-1">
                <n-input
                  v-model:value="myStyle"
                  type="textarea"
                  placeholder="可以在这里定义css样式"
                  :rows="8"
                />
              </div>
            </div>
          </div>
        </n-layout-content>
      </n-layout>
      <n-layout-sider
        collapse-mode="width"
        :collapsed-width="20"
        :width="400"
        show-trigger="arrow-circle"
        content-style="padding: 24px; max-height: 80vh;"
        bordered
        :show-collapsed-content="false"
        :native-scrollbar="false"
      >
        <div class="menu h-full space-y-4 w-full overflow-auto">
          <n-collapse default-expanded-names="0" accordion>
            <n-collapse-item title="全局设置" name="0">
              <n-space vertical>
                <n-tag>阴影个数 {{ shadowNum }}</n-tag>
                <n-slider v-model:value="shadowNum" :step="1" />
                <n-input-number v-model:value="shadowNum" size="small" />
              </n-space>
            </n-collapse-item>
            <n-collapse-item
              v-for="idx in shadowNum"
              :key="idx"
              :title="`shadow${idx}`"
              :name="`shadow${idx}`"
            >
              <n-space vertical>
                <n-tag>Offset X {{ offsetX[idx - 1] }}</n-tag>
                <n-slider v-model:value="offsetX[idx - 1]" />
                <n-input-number v-model:value="offsetX[idx - 1]" size="small" />
              </n-space>
              <n-space vertical>
                <n-tag>Offset Y {{ offsetY[idx - 1] }}</n-tag>
                <n-slider v-model:value="offsetY[idx - 1]" />
                <n-input-number v-model:value="offsetY[idx - 1]" size="small" />
              </n-space>
              <n-space vertical>
                <n-tag>blurRadius X {{ blurRadius[idx - 1] }}</n-tag>
                <n-slider v-model:value="blurRadius[idx - 1]" />
                <n-input-number v-model:value="blurRadius[idx - 1]" size="small" />
              </n-space>
              <n-space vertical>
                <n-tag>spreadRadius X {{ spreadRadius[idx - 1] }}</n-tag>
                <n-slider v-model:value="spreadRadius[idx - 1]" />
                <n-input-number v-model:value="spreadRadius[idx - 1]" size="small" />
              </n-space>
              <n-space vertical>
                <n-tag>color {{ color[idx - 1] }}</n-tag>
                <n-color-picker
                  v-model:value="color[idx - 1]"
                  show-alpha
                  :swatches="['#FFFFFF', '#18A058', '#2080F0', '#F0A020', 'rgba(248, 113, 113)']"
                />
              </n-space>
            </n-collapse-item>
            <!-- <n-collapse-item title="静态类型" name="2">
            <div>{{ boxShadow }}</div>
          </n-collapse-item> -->
          </n-collapse>
          <div v-show="shadowNum" class="code w-full overflow-auto">
            <n-code :code="`box-shadow: ${boxShadow}`" language="css" word-wrap />
          </div>
        </div>
      </n-layout-sider>
    </n-layout>
  </n-config-provider>
</template>

<script setup lang="ts">
  import {
    NTag,
    NLayout,
    NLayoutSider,
    NLayoutContent,
    NLayoutHeader,
    NSpace,
    NSlider,
    NInputNumber,
    NColorPicker,
    NCollapse,
    NCollapseItem,
    NCode,
    NConfigProvider,
    NInput,
  } from 'naive-ui';
  import hljs from 'highlight.js/lib/core';
  import css from 'highlight.js/lib/languages/css';

  import { computed, ref, watch } from 'vue';

  hljs.registerLanguage('css', css);

  const shadowNum = ref<number>(0);

  const offsetX = ref<number[]>([40]);
  const offsetY = ref<number[]>([40]);
  const blurRadius = ref<number[]>([0]);
  const spreadRadius = ref<number[]>([0]);
  const color = ref<string[]>(['rgb(248, 113, 113)']);

  watch(shadowNum, (val) => {
    if (val > 0) {
      offsetX.value = Array(val).fill(0);
      offsetY.value = Array(val).fill(0);
      blurRadius.value = Array(val).fill(0);
      spreadRadius.value = Array(val).fill(10);
      color.value = Array(val).fill('rgb(248, 113, 113)');
    }
  });

  const myStyle = ref<string>('width: 40px;\nheight: 40px;\nbackground:deeppink;\n');

  const boxShadow = computed(() => {
    return Array(shadowNum.value)
      .fill(0)
      .map(
        (item, idx) =>
          `${offsetX.value[idx]}px ${offsetY.value[idx]}px ${blurRadius.value[idx]}px ${spreadRadius.value[idx]}px ${color.value[idx]}`
      )
      .join(', ');
  });
</script>

<style lang="scss" scoped>
  #ceil {
    @apply *flex-center;
    animation: rotate infinite 0.1s linear;
    @keyframes rotate {
      0% {
        transform: rotate(0deg);
      }
      100% {
        transform: rotate(360deg);
      }
    }
  }

  .content {
    background-color: #eee;
    @apply *flex-center;
    height: calc(100vh - 80px);

    .playground {
      @apply bg-white flex flex-col;
      width: 100%;
      height: 100%;
    }
  }
</style>
