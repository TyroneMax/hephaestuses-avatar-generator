<template>
  <div class="flex flex-col gap-4">

    <!--    <div class="flex flex-col w-96">-->

    <!--      &lt;!&ndash; 自定义选项 &ndash;&gt;-->
    <!--      <div class="flex flex-col gap-2">-->
    <!--        <label class="font-bold">自定义选项</label>-->
    <!--        <div class="flex flex-col text-sm gap-2 overflow-y-scroll h-1/4 px-4">-->
    <!--          <div v-for="(value, key, index) in currentOptions" :key="key" class="">-->
    <!--            <div class="flex-col flex gap-4">-->
    <!--              <label>{{ key }}</label>-->

    <!-- 颜色选择器 -->
    <!--                  <div v-if="styleOptionsMap[key].type === 'color'" class="flex-row w-full items-center gap-4">-->
    <!--                    <Select v-model="currentOptions[key][0]" :options="styleOptionsMap[key].default" optionLabel="label"-->
    <!--                            optionValue="value" @change="updatePreviewDebounced" class="w-3/4 items-center justify-center">-->
    <!--                      <template #value="slotProps">-->
    <!--                        <div class="flex items-center gap-2">-->
    <!--                          <div class="w-4 h-4 rounded"-->
    <!--                               :style="{ backgroundColor: slotProps.value === 'transparent' ? 'transparent' : '#' + slotProps.value }"></div>-->
    <!--                          <span>{{ getColorLabel(slotProps.value) }}</span>-->
    <!--                        </div>-->
    <!--                      </template>-->
    <!--                      <template #option="slotProps">-->
    <!--                        <div class="flex items-center gap-2">-->
    <!--                          <div class="w-4 h-4 rounded"-->
    <!--                               :style="{ backgroundColor: slotProps.option.value === 'transparent' ? 'transparent' : '#' + slotProps.option.value }"></div>-->
    <!--                          <span>{{ slotProps.option.label }}</span>-->
    <!--                        </div>-->
    <!--                      </template>-->
    <!--                    </Select>-->
    <!--                    <ColorPicker v-model="currentOptions[key][0]" @hide="updatePreviewDebounced" class="ml-4"/>-->
    <!--                  </div>-->
    <!--              &lt;!&ndash; 选项下拉菜单 &ndash;&gt;-->
    <!--              <Select v-else-if="styleOptionsMap[key].type === 'select'" v-model="currentOptions[key][0]"-->
    <!--                      :options="styleOptionsMap[key].default" optionLabel="label" optionValue="value"-->
    <!--                      @change="updatePreviewDebounced" class="w-full"/>-->
    <!--              &lt;!&ndash; 滑块控制 &ndash;&gt;-->
    <!--                  <div v-else-if="styleOptionsMap[key].type === 'range'" class="w-10/12 flex flex-col gap-4">-->
    <!--                    <Slider v-model="currentOptions[key]" :min="styleOptionsMap[key].min" :max="styleOptionsMap[key].max"-->
    <!--                            :step="styleOptionsMap[key].step" @change="handleSliderInput"-->
    <!--                            @slideend="updatePreviewDebounced"/>-->
    <!--                    <div class="text-center">{{ currentOptions[key] }}</div>-->
    <!--                  </div>-->
    <!--                  &lt;!&ndash; 开关控制 &ndash;&gt;-->
    <!--                  <ToggleSwitch v-else-if="styleOptionsMap[key].type === 'switch'" v-model="currentOptions[key]"-->
    <!--                                @change="updatePreviewDebounced"/>-->
    <!-- 文本输入 -->
    <!--                  <InputText v-else-if="styleOptionsMap[key].type === 'text'" v-model="currentOptions[key]"-->
    <!--                             @input="updatePreviewDebounced" class="w-full"/>-->
    <!--                </div>-->
    <!--          </div>-->
    <!--        </div>-->
    <!--      </div>-->
    <!--    </div>-->
    <!-- 样式详情和预览 -->

    <div class="flex flex-col gap-2 flex-grow">
      <!-- 预览区域 -->
      <div class="flex flex-column justify-center mb-4">
        <img :src="avatarPreview" class="rounded-2xl w-64 h-64"/>
      </div>

    </div>
    <div class="text-center mt-2 font-bold">{{ selectedStyle }}</div>
    <Button label="生成" @click="convertToUrl"></Button>
    <div>
      <Tabs scrollable value="0" class="!bg-white">
        <TabList>
          <Tab v-for="(item,index) in styleOptions" :key="index" :value="index.toString()"
          >
            {{ item.name }}
          </Tab>
        </TabList>
        <TabPanels>
          <TabPanel value="0">
            <div class="grid grid-cols-8 gap-4">
              <div v-for="(style, index) in defaultStyles" :key="index"
                   class="flex flex-col col-span-1 items-center justify-center ">
                <img @click="selectStyle(style.value)" :src="doCreateAvatar(style)"
                     :class="style.value === selectedStyle ? 'ring-3 ring-blue-400 ring-offset-4' : ''"
                     class=" rounded-2xl w-24 h-24 hover:ring-3 cursor-pointer hover:ring-blue-400 hover:ring-offset-4"/>
              </div>
            </div>
          </TabPanel>
          <TabPanel value="1">
            <div class="grid grid-cols-8 gap-4 ">
              <div class="ol-span-1 items-center justify-center relative">
                <div class="items-center flex justify-center">
                  <img @click="toggleBackgroundColorPicker"
                       :src="getAvatarWithCurrentOption()"
                       :class="currentOptions['backgroundColor'][0] === this.defaultStyles.find(style => style.value === this.selectedStyle)['config']['backgroundColor'][0] ? 'ring-3 ring-blue-400 ring-offset-4' : ''"
                       class=" !rounded-2xl w-24 h-24 hover:ring-3 cursor-pointer z-0"/>
                </div>
                <i class="pi pi-palette absolute z-2 inset-x-20  inset-y-16" style="font-size: 1.5rem"></i>
                <ColorPicker v-if="backgroundColorPickerVisible"
                             v-model="currentOptions['backgroundColor'][0]"
                             inline
                             @update:modelValue="updatePreviewAfterColorChange"
                             class=" inset-24 !absolute z-10"/>
              </div>
              <div v-for="(option, index) in currentStyleConfig.originalOptions.get('backgroundColor').default"
                   :key="index"
                   class="flex flex-col col-span-1 items-center justify-center ">
                <img @click="selectOption('backgroundColor',option.value)"
                     :class="this.defaultStyles.find(style => style.value === this.selectedStyle)['config']['backgroundColor'][0] === option.value? 'ring-3 ring-blue-400 ring-offset-4' : ''"
                     :src="doCreateAvatarWithSpecConfig('backgroundColor',option.value)"
                     class=" rounded-2xl w-24 h-24 hover:ring-3 cursor-pointer hover:ring-blue-400 hover:ring-offset-4"/>
              </div>
            </div>
          </TabPanel>
          <TabPanel v-for="(item, index) in styleOptions.slice(2)" :key="index+2" :value="(index+2).toString()"
          >
            <div v-if="item.type==='select'" class="grid grid-cols-8 gap-4 ">
              <div v-for="(option, index) in currentStyleConfig.originalOptions.get(item.name).default"
                   :key="index"
                   class="flex flex-col col-span-1 items-center justify-center ">
                <img @click="selectOption(item.name,option.value)"
                     :class="this.defaultStyles.find(style => style.value === this.selectedStyle)['config'][item.name][0] === option.value? 'ring-3 ring-blue-400 ring-offset-4' : ''"
                     :src="doCreateAvatarWithSpecConfig(item.name,option.value)"
                     class=" rounded-2xl w-24 h-24 hover:ring-3 cursor-pointer hover:ring-blue-400 hover:ring-offset-4"/>
              </div>
            </div>
            <div v-if="item.type==='color'">
              <div class="ol-span-1 items-center justify-center relative">
                <div class="items-center flex justify-center">
                  <img @click="toggleBackgroundColorPicker"
                       :src="getAvatarWithCurrentOption()"
                       :class="currentOptions[item.name][0] === this.defaultStyles.find(style => style.value === this.selectedStyle)['config'][item.name][0] ? 'ring-3 ring-blue-400 ring-offset-4' : ''"
                       class=" !rounded-2xl w-24 h-24 hover:ring-3 cursor-pointer z-0"/>
                </div>
                <i class="pi pi-palette absolute z-2 inset-x-20  inset-y-16" style="font-size: 1.5rem"></i>
                <ColorPicker v-if="backgroundColorPickerVisible"
                             v-model="currentOptions[item.name][0]"
                             inline
                             @update:modelValue="updatePreviewAfterColorChange"
                             class=" inset-24 !absolute z-10"/>
              </div>
              <div v-for="(option, index) in currentStyleConfig.originalOptions.get(item.name).default"
                   :key="index"
                   class="flex flex-col col-span-1 items-center justify-center ">
                <img @click="selectOption(item.name ,option.value)"
                     :class="this.defaultStyles.find(style => style.value === this.selectedStyle)['config'][item.name][0] === option.value? 'ring-3 ring-blue-400 ring-offset-4' : ''"
                     :src="doCreateAvatarWithSpecConfig(item.name ,option.value)"
                     class=" rounded-2xl w-24 h-24 hover:ring-3 cursor-pointer hover:ring-blue-400 hover:ring-offset-4"/>
              </div>
            </div>
            <div v-if="item.type==='range'" class="m-4  flex flex-row gap-8 justify-items-start items-center">
              <InputText v-model="defaultStyles.find(style => style.value === this.selectedStyle)['config'][item.name]"
                         class="!w-20"/>
              <div class="w-48 flex flex-col justify-center">
                <Slider v-model="defaultStyles.find(style => style.value === this.selectedStyle)['config'][item.name]"
                        :min="defaultStyles.find(style => style.value === this.selectedStyle)['config'][item.min]"
                        :max="defaultStyles.find(style => style.value === this.selectedStyle)['config'][item.max]"
                        :step="defaultStyles.find(style => style.value === this.selectedStyle)['config'][item.step]"
                        @change="handleSliderInput"
                />
              </div>
            </div>
            <div v-if="item.type==='switch'" class="m-4">

              <ToggleSwitch
                  v-model="defaultStyles.find(style => style.value === this.selectedStyle)['config'][item.name]"
                  @change="updatePreviewDebounced"/>
            </div>
          </TabPanel>
        </TabPanels>
      </Tabs>
    </div>
  </div>
</template>

<script>
import * as dicebearCollection from '@dicebear/collection';
import {createAvatar, schema} from '@dicebear/core';

export default {
  name: 'AvatarSelector',
  props: {
    modelValue: {
      type: Boolean,
      default: true
    },
    title: {
      type: String,
      default: '选择头像'
    }
  },
  emits: ['update:modelValue', 'select'],

  data() {
    return {
      //当前的style确定配置
      currentStyleConfig: {},
      //当前的style里面拥有的option
      currentOptions: {},
      customAvatar: {},
      selectedStyle: 'adventurer',
      currentStyleOriginalOptions: {},


      visible: this.modelValue,
      backgroundColorPickerVisible: false,


      activeCategory: 'all',
      debounceTimer: null,
      isUpdating: false,

      styleOptions: [
        {name: 'Styles', type: 'select', default: false},
        {name: 'Background Color', type: 'select', default: false}
      ],
      styleOptionsMap: {},
      defaultStyles: [
        {label: 'Adventurer', value: 'adventurer'},
        {label: 'Adventurer Neutral', value: 'adventurerNeutral'},
        {label: 'Avataaars', value: 'avataaars'},
        {label: 'Avataaars Neutral', value: 'avataaarsNeutral'},
        {label: 'Big Ears', value: 'bigEars'},
        {label: 'Big Ears Neutral', value: 'bigEarsNeutral'},
        {label: 'Big Smile', value: 'bigSmile'},
        {label: 'Bottts', value: 'bottts'},
        {label: 'Bottts Neutral', value: 'botttsNeutral'},
        {label: 'Croodles', value: 'croodles'},
        {label: 'Croodles Neutral', value: 'croodlesNeutral'},
        {label: 'Dylan', value: 'dylan'},
        {label: 'Fun Emoji', value: 'funEmoji'},
        {label: 'Glass', value: 'glass'},
        {label: 'Icons', value: 'icons'},
        {label: 'Identicon', value: 'identicon'},
        {label: 'Initials', value: 'initials'},
        {label: 'Lorelei', value: 'lorelei'},
        {label: 'Lorelei Neutral', value: 'loreleiNeutral'},
        {label: 'Micah', value: 'micah'},
        {label: 'Miniavs', value: 'miniavs'},
        {label: 'Notionists', value: 'notionists'},
        {label: 'Notionists Neutral', value: 'notionistsNeutral'},
        {label: 'Open Peeps', value: 'openPeeps'},
        {label: 'Personas', value: 'personas'},
        {label: 'Pixel Art', value: 'pixelArt'},
        {label: 'Pixel Art Neutral', value: 'pixelArtNeutral'},
        {label: 'Rings', value: 'rings'},
        {label: 'Shapes', value: 'shapes'},
        {label: 'Thumbs', value: 'thumbs'}
      ],
      defaultColors: [
        {label: '浅蓝绿', value: '#80cbc4'},
        {label: '浅天蓝', value: '#80deea'},
        {label: '天空蓝', value: '#81d4fa'},
        {label: '淡蓝色', value: '#90caf9'},
        {label: '淡紫蓝', value: '#9fa8da'},
        {label: '浅绿色', value: '#a5d6a7'},
        {label: '淡紫色', value: '#b39ddb'},
        {label: '浅黄绿', value: '#c5e1a5'},
        {label: '薰衣草', value: '#ce93d8'},
        {label: '浅黄色', value: '#e6ee9c'},
        {label: '浅红色', value: '#ef9a9a'},
        {label: '粉红色', value: '#f48fb1'},
        {label: '浅橙色', value: '#ffab91'},
        {label: '浅橘黄', value: '#ffcc80'},
        {label: '浅金黄', value: '#ffe082'},
        {label: '淡粉色', value: '#ffd5dc'},
        {label: '浅肤色', value: '#ffdfbf'}
      ],
      commonColors: [
        {label: '透明', value: 'transparent'},
        {label: '浅蓝绿', value: '#80cbc4'},
        {label: '浅天蓝', value: '#80deea'},
        {label: '天空蓝', value: '#81d4fa'},
        {label: '淡蓝色', value: '#90caf9'},
        {label: '淡紫蓝', value: '#9fa8da'},
        {label: '浅绿色', value: '#a5d6a7'},
        {label: '淡紫色', value: '#b39ddb'},
        {label: '浅黄绿', value: '#c5e1a5'},
        {label: '薰衣草', value: '#ce93d8'},
        {label: '浅黄色', value: '#e6ee9c'},
        {label: '浅红色', value: '#ef9a9a'},
        {label: '粉红色', value: '#f48fb1'},
        {label: '浅橙色', value: '#ffab91'},
        {label: '浅橘黄', value: '#ffcc80'},
        {label: '浅金黄', value: '#ffe082'},
        {label: '淡粉色', value: '#ffd5dc'},
        {label: '浅肤色', value: '#ffdfbf'}
      ]
    };
  },
  computed: {
    currentStyle() {
      return this.currentStyleConfig
    },
    avatarPreview() {
      return this.getPreview();
    }
  },
  watch: {
    currentOptions: {
      handler() {
        this.updatePreviewDebounced();
      },
      deep: true
    },
    selectedStyle() {
      this.updatePreviewDebounced();
    },
    defaultStyles() {
      this.updatePreviewDebounced();
    }
  },

  methods: {

    toggleBackgroundColorPicker() {
      this.backgroundColorPickerVisible = !this.backgroundColorPickerVisible;

      // 如果选择器打开，添加点击监听器
      if (this.backgroundColorPickerVisible) {
        // 使用 nextTick 确保 DOM 更新后再添加事件监听
        this.$nextTick(() => {
          document.addEventListener('click', this.handleOutsideClick);
        });
      }
    },

    updatePreviewAfterColorChange(value) {
      this.defaultStyles.find(style => style.value === this.selectedStyle).config.backgroundColor[0] = value;
      this.updatePreviewDebounced();
      // 不立即关闭，让用户可以继续选择
    },

    openBackgroundColorPicker() {
      this.backgroundColorPickerVisible = true
    },
    getAvatarWithCurrentOption() {
      let dicebearCollectionElement = dicebearCollection[this.selectedStyle];
      let config = createAvatar(dicebearCollectionElement, {
        ...this.defaultStyles.find(style => style.value === this.selectedStyle).config,
      });
      return config.toDataUri();
    },
    doCreateAvatarWithSpecConfig(optionKey, optionValue) {
      let dicebearCollectionElement = dicebearCollection[this.selectedStyle];
      let config = createAvatar(dicebearCollectionElement, {
        ...this.defaultStyles.find(style => style.value === this.selectedStyle).config,
        [optionKey]: [optionValue]
      });
      return config.toDataUri();
    },
    doCreateAvatar(style) {
      let dicebearCollectionElement = dicebearCollection[style.value];
      let config = createAvatar(dicebearCollectionElement, {
        // ...this.currentOptions,
        ...style.config
      });
      return config.toDataUri();
    },

    getPreview() {
      let config = createAvatar(dicebearCollection[this.selectedStyle], {
        ...this.defaultStyles.find(style => style.value === this.selectedStyle).config,
      });
      return config.toDataUri();
    },
    convertToUrl() {
      // 构建 dicebear HTTP API URL
      let apiUrl = `https://api.dicebear.com/9.x/${this.kebabCase(this.selectedStyle)}/svg`;

      // 构建查询参数
      const queryParams = new URLSearchParams();
      for (const [key, value] of Object.entries(this.currentOptions)) {
        if (value !== undefined && value !== null) {
          queryParams.append(key, value);
        }
      }
      // 组合最终URL
      const finalUrl = `${apiUrl}?${queryParams.toString()}`;
      // this.customAvatar = finalUrl;
      window.open(finalUrl);
    },

    selectStyle(styleValue) {
      this.selectedStyle = styleValue || this.selectedStyle;
      this.loadStyleOptions(this.selectedStyle);
      this.currentStyleConfig = this.defaultStyles.find(style => style.value === this.selectedStyle);

      console.log(this.currentStyleConfig)
      console.log(this.styleOptions)
    },
    selectOption(key, value) {
      console.log();
      console.log(this.defaultStyles[this.selectedStyle]);
      this.defaultStyles.find(style => style.value === this.selectedStyle)['config'][key] = [value];

    },
    resetStyleOptions() {
      this.styleOptions = [{name: 'Styles', type: 'select', default: false}, {
        name: 'Background Color',
        type: 'select',
        default: false
      }];
    },
    loadStyleOptions(styleValue) {
      // 重置自定义选项
      this.currentOptions = {};

      this.resetStyleOptions();
      let originalOptions = this.getStyleOptions(styleValue);
      this.styleOptions = [...this.styleOptions, ...originalOptions];

      console.log(this.styleOptions);

      this.styleOptionsMap = this.styleOptions.reduce((map, item) => {
        map[item.name] = item;
        return map;
      }, {});
      for (let i = 0; i < this.styleOptions.length; i++) {
        if (i <= 1) continue;
        let option = this.styleOptions[i];
        if (option.type === 'select') {
          this.currentOptions[option.name] = [option.default[Math.floor(Math.random() * option.default.length)].value];
        } else if (option.type === 'color') {
          this.currentOptions[option.name] = [option.default[Math.floor(Math.random() * option.default.length)].value];
        } else {
          this.currentOptions[option.name] = option.default;
        }
      }
      // 设置固定大小
      this.currentOptions['size'] = 128;

    },
    initDefaultStyleOptions(styleName) {
      let originalOptions = this.getStyleOptions(styleName);
      let convertedOptions = {};
      for (let i = 0; i < originalOptions.length; i++) {
        if (i <= 1) continue;
        let option = originalOptions[i];
        if (option.type === 'select') {
          convertedOptions[option.name] = [option.default[Math.floor(Math.random() * option.default.length)].value];
        } else if (option.type === 'color') {
          convertedOptions[option.name] = [option.default[Math.floor(Math.random() * option.default.length)].value];
        } else {
          convertedOptions[option.name] = option.default;
        }
      }
      convertedOptions['size'] = 128;
      console.log(convertedOptions);
      return {originalOptions, convertedOptions};
    },
    getStyleOptions(styleName) {
      // 获取指定样式支持的所有选项
      try {
        // 使用DiceBear collection中的样式
        const style = dicebearCollection[styleName];
        if (!style || !style.schema || !style.schema.properties) {

          return [];
        }
        // 合并核心选项和样式特定选项


        const combinedProperties = {
          ...schema.properties,
          ...style.schema.properties
        };

        // 转换选项为UI配置
        return this.transformOptionsToUI(combinedProperties);
      } catch (error) {

        return [];
      }
    },
    transformOptionsToUI(properties) {
      const uiOptions = [];

      const defaultColors = this.commonColors.map((color) => {
        return {label: color.label, value: this.formatColor(color.value)};
      });
      for (const [key, config] of Object.entries(properties)) {
        // handle spec options
        if (key.endsWith('Color')) {
          const uiOption = {
            name: key,
            type: 'color',
            default: defaultColors
          };
          uiOptions.push(uiOption);
          continue;
        } else if (key === 'backgroundRotation') {
          const uiOption = {
            name: key,
            type: 'backgroundRotation',
            default: 0,
            min: config.minimum !== undefined ? config.minimum : 0,
            max: config.maximum !== undefined ? config.maximum : 100,
            step: config.multipleOf || 1
          };
        } else if (key === 'seed' && this.selectedStyle === 'initials') {
          const uiOption = {
            name: key,
            type: 'text',
            default: config.default !== undefined ? config.default : ''
          };
          uiOptions.push(uiOption);
        } else if (key === 'base') {
          continue;
        } else {
          // handle common options
          if (config.type === 'boolean') {
            const uiOption = {
              name: key,
              type: 'switch',
              default: config.default !== undefined ? config.default : true
            };
            uiOptions.push(uiOption);
          }
          if (config.type === 'integer') {
            const uiOption = {
              name: key,
              type: 'range',
              default: config.default !== undefined ? config.default : 0,
              min: config.minimum !== undefined ? config.minimum : 0,
              max: config.maximum !== undefined ? config.maximum : 100,
              step: config.multipleOf || 1
            };
            uiOptions.push(uiOption);
          }
          if (config.type === 'array') {
            let option = config.default !== undefined ? config.default.sort() : [];
            const arr = [];
            for (let i = 0; i < option.length; i++) {
              arr.push({label: option[i], value: option[i]});
            }
            const uiOption = {
              name: key,
              type: 'select',
              default: arr
            };
            uiOptions.push(uiOption);
          }
        }
      }
      return uiOptions;
    },
    kebabCase(str) {
      // 将驼峰命名转换为kebab-case（用于URL参数）
      return str.replace(/([a-z0-9])([A-Z])/g, '$1-$2').toLowerCase();
    },
    updatePreviewDebounced() {
      // 防抖更新预览
      if (this.debounceTimer) {
        clearTimeout(this.debounceTimer);
      }
      this.debounceTimer = setTimeout(() => {
        this.isUpdating = false;
        // 更新预览
        this.$forceUpdate(); // 强制组件重新渲染，触发计算属性 avatarPreview
      }, 300); // 300ms防抖时间
    },
    handleOutsideClick(event) {
      // 获取颜色选择器元素
      const colorPicker = document.querySelector('.p-colorpicker');
      // 获取触发颜色选择器的图片元素
      const imgTrigger = document.querySelector('[class*="!rounded-2xl w-24 h-24 hover:ring-3 cursor-pointer z-0"]');

      // 如果点击的不是颜色选择器内部元素且不是触发的图片，则关闭选择器
      if (colorPicker &&
          !colorPicker.contains(event.target) &&
          (!imgTrigger || !imgTrigger.contains(event.target))) {
        this.backgroundColorPickerVisible = false;
        // 移除事件监听器
        document.removeEventListener('click', this.handleOutsideClick);
      }
    },
    handleSliderInput() {
      // 当滑块正在移动时，标记为正在更新
      this.isUpdating = true;

      // 设置防抖计时器
      if (this.debounceTimer) {
        clearTimeout(this.debounceTimer);
      }
      this.debounceTimer = setTimeout(() => {
        this.isUpdating = false;
        this.debounceTimer = null;
      }, 300); // 300ms防抖时间
    },
    formatColor(color) {
      // 处理透明色
      if (color === 'transparent') return 'transparent';

      // 确保颜色是6位十六进制（不带#号）
      if (color.startsWith('#')) {
        return color.substring(1);
      }

      // 已经是正确格式的颜色代码
      if (/^[a-fA-F0-9]{6}$/.test(color)) {
        return color;
      }

      // 默认返回白色
      return 'ffffff';
    },
    getRandomColor() {
      return this.defaultColors[Math.floor(Math.random() * this.defaultColors.length)].value.replace('#', '');
    },
    getColorLabel(colorValue) {
      if (!colorValue) return '选择颜色';
      if (colorValue === 'transparent') return '透明';
      const color = this.commonColors.find((c) => c.value === '#' + colorValue);
      return color ? color.label : '自定义颜色';
    }
  },
  created() {

    this.defaultStyles = this.defaultStyles.map(style => {
      let {originalOptions, convertedOptions} = this.initDefaultStyleOptions(style.value);

      return {
        ...style,
        originalOptions: originalOptions.reduce((acc, item) => {
          acc.set(item.name, item);
          return acc;
        }, new Map()),

        config: {
          seed: "Tyrone",
          ...convertedOptions
        }
      };
    });
    console.log(this.defaultStyles)
    this.selectStyle();

  },
  beforeUnmount() {
    document.removeEventListener('click', this.handleOutsideClick);
  },

};
</script>

<style scoped></style>
