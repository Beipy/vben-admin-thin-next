<template>
  <div v-if="getShowDarkModeToggle" :class="getClass" @click="toggleDarkMode">
    <div :class="`${prefixCls}-inner`"> </div>
    <SvgIcon size="12" name="sun" />
    <SvgIcon size="12" name="moon" />
  </div>
</template>
<script lang="ts">
  import { defineComponent, computed, unref } from 'vue';
  import { SvgIcon } from '/@/components/Icon';
  import { useDesign } from '/@/hooks/web/useDesign';
  import { useRootSetting } from '/@/hooks/setting/useRootSetting';
  import { updateHeaderBgColor, updateSidebarBgColor } from '/@/logics/theme/updateBackground';
  import { updateDarkTheme } from '/@/logics/theme/dark';
  import { ThemeEnum } from '/@/enums/appEnum';

  export default defineComponent({
    name: 'DarkModeToggle',
    components: { SvgIcon },
    setup() {
      const { prefixCls } = useDesign('dark-switch');
      const { getDarkMode, setDarkMode, getShowDarkModeToggle } = useRootSetting();

      const isDark = computed(() => getDarkMode.value === ThemeEnum.DARK);

      const getClass = computed(() => [
        prefixCls,
        {
          [`${prefixCls}--dark`]: unref(isDark),
        },
      ]);

      function toggleDarkMode() {
        const darkMode = getDarkMode.value === ThemeEnum.DARK ? ThemeEnum.LIGHT : ThemeEnum.DARK;
        setDarkMode(darkMode);
        updateDarkTheme(darkMode);
        updateHeaderBgColor();
        updateSidebarBgColor();
      }

      return {
        getClass,
        isDark,
        prefixCls,
        toggleDarkMode,
        getShowDarkModeToggle,
      };
    },
  });
</script>
<style lang="less" scoped>
  @prefix-cls: ~'@{namespace}-dark-switch';

  html[data-theme='dark'] {
    .@{prefix-cls} {
      border: 1px solid rgb(196, 188, 188);
    }
  }

  .@{prefix-cls} {
    position: relative;
    display: flex;
    width: 40px;
    height: 20px;
    padding: 0 4px;
    margin-left: auto;
    cursor: pointer;
    background-color: #151515;
    border-radius: 30px;
    justify-content: space-between;
    align-items: center;

    &-inner {
      position: absolute;
      z-index: 1;
      width: 14px;
      height: 14px;
      background-color: #fff;
      border-radius: 50%;
      transition: transform 0.5s, background-color 0.5s;
      will-change: transform;
    }

    &--dark {
      .@{prefix-cls}-inner {
        transform: translateX(calc(100% + 2px));
      }
    }
  }
</style>
