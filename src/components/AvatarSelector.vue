<template>
  <div class="flex flex-col gap-4">
    <div class="flex flex-col gap-2 flex-grow">
      <!-- 预览区域 -->
      <div class="flex flex-column justify-center mb-4">
        <img :src="avatarPreview" class="rounded-2xl w-64 h-64"/>
      </div>

    </div>
    <Button label="Generate" @click="convertToUrl"></Button>
    <div>
      <Tabs scrollable value="0" class="!bg-white">
        <TabList>
          <Tab v-for="(item,index) in styleOptions.filter(item => !item.name.includes('Probability'))" :key="index"
               :value="index.toString()"
          >
            {{ formatOptionName(item.name) }}
          </Tab>
        </TabList>
        <TabPanels>
          <TabPanel value="0">
            <div class="grid grid-cols-2 sm:grid-cols-6 lg:grid-cols-8 xl:grid-cols-8 gap-4">
              <div v-for="(style, index) in defaultStyles" :key="index"
                   class="flex flex-col col-span-1 items-center justify-center ">
                <img @click="selectStyle(style.value)" :src="doCreateAvatar(style)"
                     :class="style.value === selectedStyle ? 'ring-3 ring-blue-400 ring-offset-4' : ''"
                     class=" rounded-2xl w-24 h-24 hover:ring-3 cursor-pointer hover:ring-blue-400 hover:ring-offset-4"/>
              </div>
            </div>
          </TabPanel>
          <TabPanel value="1">
            <div class="grid grid-cols-2 sm:grid-cols-6 lg:grid-cols-8 xl:grid-cols-8 gap-4 ">
              <div class="col-span-1 items-center justify-center flex flex-row ">
                <div class="items-center relative inline-flex justify-center">
                  <img
                      @click="toggleColorPicker(this.selectedStyle+`backgroundColor`, $event)"
                      :src="getAvatarWithCurrentOption()"
                      :class="currentOptions['backgroundColor'][0] === this.defaultStyles.find(style => style.value === this.selectedStyle)['config']['backgroundColor'][0] ? 'ring-3 ring-blue-400 ring-offset-4' : ''"
                      class=" !rounded-2xl w-24 h-24 left-1/2  hover:ring-3 hover:ring-blue-400 hover:ring-offset-4 cursor-pointer z-0"/>
                  <svg t="1748095098755" @click="toggleColorPicker(this.selectedStyle+`backgroundColor`, $event)"
                       class="absolute inset-y-15 inset-x-15 z-10 cursor-pointer"
                       style="position: absolute ;font-size: 1.5rem; z-index: 20; width: 2rem;height: 2rem"
                       viewBox="0 0 1024 1024" version="1.1" xmlns="http://www.w3.org/2000/svg" p-id="9018" width="200"
                       height="200">
                    <path
                        d="M311.224 428.616V40.96C186.696 93.99 87.406 195.29 36.936 321.096l274.286 274.286 0.002-166.766z"
                        fill="#AFE164" p-id="9019"></path>
                    <path
                        d="M428.616 311.22L702.904 36.936C643.84 13.164 579.474 0 512 0c-71.314 0-139.154 14.628-200.776 40.96v387.656L428.616 311.22z"
                        fill="#D7E664" p-id="9020"></path>
                    <path
                        d="M983.04 311.22H595.384l117.394 117.396 274.286 274.286C1010.836 643.84 1024 579.474 1024 512c0-71.314-14.628-139.154-40.96-200.78z"
                        fill="#FFAA64" p-id="9021"></path>
                    <path
                        d="M712.776 595.384V983.04c124.526-53.03 223.816-154.33 274.286-280.136L712.776 428.616v166.768z"
                        fill="#FF6469" p-id="9022"></path>
                    <path
                        d="M595.384 712.776L321.096 987.064C380.16 1010.834 444.526 1024 512 1024c71.316 0 139.154-14.628 200.776-40.96V595.384L595.384 712.776z"
                        fill="#C3B4FA" p-id="9023"></path>
                    <path
                        d="M428.616 712.776H40.96c53.03 124.526 154.33 223.816 280.136 274.286l274.286-274.286H428.616z"
                        fill="#7DD2F0" p-id="9024"></path>
                    <path
                        d="M311.224 595.384L36.936 321.096C13.164 380.16 0 444.526 0 512c0 71.314 14.628 139.154 40.96 200.776h387.656L311.224 595.384z"
                        fill="#7DE6C3" p-id="9025"></path>
                    <path d="M595.384 311.22H983.04C930.01 186.696 828.71 87.404 702.904 36.936L428.616 311.22h166.768z"
                          fill="#FFE669" p-id="9026"></path>
                  </svg>
                </div>

                <Popover :ref="this.selectedStyle+`backgroundColor`">
                  <ColorPicker inline v-model="currentOptions['backgroundColor'][0]"
                               @update:modelValue="updatePreviewAfterColorChange($event, 'backgroundColor')"/>
                </Popover>

              </div>
              <div v-for="(option, index) in currentStyleConfig.originalOptions.get('backgroundColor').default"
                   :key="index"
                   class="flex flex-col col-span-1 items-center justify-center ">
                <img @click="selectOption('backgroundColor',option.value)"
                     :class="[this.defaultStyles.find(style => style.value === this.selectedStyle)['config']['backgroundColor'][0] === option.value? 'ring-3 ring-blue-400 ring-offset-4' : '',
                     option.value === 'transparent' ? 'chessboard-bg' : '']"
                     :src="doCreateAvatarWithSpecConfig('backgroundColor',option.value)"
                     class=" rounded-2xl w-24 h-24 hover:ring-3 cursor-pointer hover:ring-blue-400 hover:ring-offset-4"/>
              </div>
            </div>
          </TabPanel>
          <TabPanel v-for="(item, index) in styleOptions.slice(2).filter(item => !item.name.includes('Probability'))"
                    :key="index+2" :value="(index+2).toString()"
          >
            <div v-if="item.type==='select'"
                 class="grid grid-cols-2 sm:grid-cols-6 lg:grid-cols-8 xl:grid-cols-8 gap-4 ">
              <div class="col-span-1 flex  items-center justify-center"
                   v-if="this.defaultStyles.find(style => style.value === this.selectedStyle)['config'][item.name+'Probability'] !== undefined">
                <img
                    @click="selectOption(item.name+'Probability',0)"
                    :class="this.defaultStyles.find(style => style.value === this.selectedStyle)['config'][item.name+'Probability'] === 0? 'ring-3 ring-blue-400 ring-offset-4' : ''"
                    :src="doCreateAvatarWithSpecConfig(item.name,this.defaultStyles.find(style => style.value === this.selectedStyle)['config'][item.name][0],0)"
                    class=" rounded-2xl w-24 h-24 hover:ring-3 cursor-pointer hover:ring-blue-400 hover:ring-offset-4"/>
              </div>
              <div v-for="(option, index) in currentStyleConfig.originalOptions.get(item.name).default"
                   :key="index"
                   class="flex flex-col col-span-1 items-center justify-center ">
                <img @click="selectOption(item.name,option.value)"
                     :class="this.defaultStyles.find(style => style.value === this.selectedStyle)['config'][item.name][0] === option.value &&
                     this.defaultStyles.find(style => style.value === this.selectedStyle)['config'][item.name+'Probability']? 'ring-3 ring-blue-400 ring-offset-4' : ''"
                     :src="doCreateAvatarWithSpecConfig(item.name,option.value)"
                     class=" rounded-2xl w-24 h-24 hover:ring-3 cursor-pointer hover:ring-blue-400 hover:ring-offset-4"/>
              </div>
            </div>
            <div v-if="item.type==='color'" class="grid grid-cols-2 sm:grid-cols-6 lg:grid-cols-8 xl:grid-cols-8 gap-4">
              <div class="items-center justify-center flex flex-row">
                <div class=" col-span-1 items-center relative justify-center">
                  <img
                      @click="toggleColorPicker(this.selectedStyle+item.name, $event)"
                      :src="getAvatarWithCurrentOption()"
                      :class="[currentOptions[item.name][0] === this.defaultStyles.find(style => style.value === this.selectedStyle)['config'][item.name][0] ? 'ring-3 ring-blue-400 ring-offset-4' : '']"
                      class=" !rounded-2xl w-24 h-24 hover:ring-3 ring-offset-4 ring-blue-400 cursor-pointer z-0"/>
                  <svg t="1748095098755" @click="toggleColorPicker(this.selectedStyle+`backgroundColor`, $event)"
                       class="inset-y-15 inset-x-15 z-10 absolute cursor-pointer"
                       style="position: absolute ;font-size: 1.5rem; z-index: 20; width: 2rem;height: 2rem"
                       viewBox="0 0 1024 1024" version="1.1" xmlns="http://www.w3.org/2000/svg" p-id="9018" width="200"
                       height="200">
                    <path
                        d="M311.224 428.616V40.96C186.696 93.99 87.406 195.29 36.936 321.096l274.286 274.286 0.002-166.766z"
                        fill="#AFE164" p-id="9019"></path>
                    <path
                        d="M428.616 311.22L702.904 36.936C643.84 13.164 579.474 0 512 0c-71.314 0-139.154 14.628-200.776 40.96v387.656L428.616 311.22z"
                        fill="#D7E664" p-id="9020"></path>
                    <path
                        d="M983.04 311.22H595.384l117.394 117.396 274.286 274.286C1010.836 643.84 1024 579.474 1024 512c0-71.314-14.628-139.154-40.96-200.78z"
                        fill="#FFAA64" p-id="9021"></path>
                    <path
                        d="M712.776 595.384V983.04c124.526-53.03 223.816-154.33 274.286-280.136L712.776 428.616v166.768z"
                        fill="#FF6469" p-id="9022"></path>
                    <path
                        d="M595.384 712.776L321.096 987.064C380.16 1010.834 444.526 1024 512 1024c71.316 0 139.154-14.628 200.776-40.96V595.384L595.384 712.776z"
                        fill="#C3B4FA" p-id="9023"></path>
                    <path
                        d="M428.616 712.776H40.96c53.03 124.526 154.33 223.816 280.136 274.286l274.286-274.286H428.616z"
                        fill="#7DD2F0" p-id="9024"></path>
                    <path
                        d="M311.224 595.384L36.936 321.096C13.164 380.16 0 444.526 0 512c0 71.314 14.628 139.154 40.96 200.776h387.656L311.224 595.384z"
                        fill="#7DE6C3" p-id="9025"></path>
                    <path d="M595.384 311.22H983.04C930.01 186.696 828.71 87.404 702.904 36.936L428.616 311.22h166.768z"
                          fill="#FFE669" p-id="9026"></path>
                  </svg>
                </div>

                <Popover :ref="this.selectedStyle+item.name">
                  <ColorPicker v-model="currentOptions[item.name][0]"
                               inline
                               @update:modelValue="updatePreviewAfterColorChange($event, item.name)"/>
                </Popover>
              </div>
              <div v-for="(option, index) in currentStyleConfig.originalOptions.get(item.name).default"
                   :key="index"

                   class="flex flex-col col-span-1 items-center justify-center ">
                <img @click="selectOption(item.name ,option.value)"
                     :class="[this.defaultStyles.find(style => style.value === this.selectedStyle)['config'][item.name][0] === option.value? 'ring-3 ring-blue-400 ring-offset-4' : '',
                              option.value === 'transparent' ? 'chessboard-bg' : '']"
                     :src="doCreateAvatarWithSpecConfig(item.name ,option.value)"
                     class=" rounded-2xl w-24 h-24 hover:ring-3 cursor-pointer hover:ring-blue-400 hover:ring-offset-4"/>
              </div>
            </div>
            <div v-if="item.type==='range'" class="m-4  flex flex-row gap-8 justify-items-start items-center">
              <InputNumber
                  v-model="defaultStyles.find(style => style.value === this.selectedStyle)['config'][item.name]"></InputNumber>
              <div class="w-48 flex flex-col justify-center">
                <Slider v-model="defaultStyles.find(style => style.value === this.selectedStyle)['config'][item.name]"
                        :min="defaultStyles.find(style => style.value === this.selectedStyle)['config'][item.min]"
                        :max="defaultStyles.find(style => style.value === this.selectedStyle)['config'][item.max]"
                        :step="defaultStyles.find(style => style.value === this.selectedStyle)['config'][item.step]"
                />
              </div>
            </div>
            <div v-if="item.type==='switch'" class="m-4">
              <ToggleSwitch
                  v-model="defaultStyles.find(style => style.value === this.selectedStyle)['config'][item.name]"
              />
            </div>
            <div v-if="item.type==='text'" class="m-4">
              <InputText
                  v-model="defaultStyles.find(style => style.value === this.selectedStyle)['config'][item.name]"
              />
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
  data() {
    return {
      currentStyleConfig: {},
      currentOptions: {},
      customAvatar: {},
      selectedStyle: 'adventurer',
      styleOptions: [
        {name: 'Styles', type: 'select', default: false},
        {name: 'Background Color', type: 'select', default: false}
      ],
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
      commonColors: [
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
        {label: '浅肤色', value: '#ffdfbf'},
        {label: '透明', value: 'transparent'},
      ],
    };
  },
  computed: {
    avatarPreview() {
      return this.getPreview();
    }
  },
  methods: {
    toggleColorPicker(id, event) {
      let op = this.$refs[id] ? this.$refs[id] : null;
      if (op) {
        if (Array.isArray(op)) {
          op = op[0];
        }
        op.toggle(event);
      }
    },
    updatePreviewAfterColorChange(value, itemName) {
      if (itemName === 'backgroundColor') {
        this.emitBackgroundColor();
      }
      this.defaultStyles.find(style => style.value === this.selectedStyle).config[itemName][0] = value;
    },
    getAvatarWithCurrentOption() {
      let dicebearCollectionElement = dicebearCollection[this.selectedStyle];
      let config = createAvatar(dicebearCollectionElement, {
        ...this.defaultStyles.find(style => style.value === this.selectedStyle).config,
      });
      return config.toDataUri();
    },
    doCreateAvatarWithSpecConfig(optionKey, optionValue, probability = 100) {
      let dicebearCollectionElement = dicebearCollection[this.selectedStyle];
      let config = createAvatar(dicebearCollectionElement, {
        ...this.defaultStyles.find(style => style.value === this.selectedStyle).config,
        [optionKey]: [optionValue],
        [`${optionKey}Probability`]: probability
      });
      return config.toDataUri();
    },
    doCreateAvatar(style) {
      let dicebearCollectionElement = dicebearCollection[style.value];
      let config = createAvatar(dicebearCollectionElement, {
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
      let apiUrl = `https://api.dicebear.com/9.x/${this.kebabCase(this.selectedStyle)}/svg`;
      const queryParams = new URLSearchParams();
      for (const [key, value] of Object.entries(this.defaultStyles[this.defaultStyles.findIndex(style => style.value === this.selectedStyle)].config)) {
        if (value !== undefined && value !== null) {
          queryParams.append(key, value);
        }
      }
      const finalUrl = `${apiUrl}?${queryParams.toString()}`;
      window.open(finalUrl);
    },
    selectStyle(styleValue) {
      this.selectedStyle = styleValue || this.selectedStyle;
      this.loadStyleOptions(this.selectedStyle);
      this.currentStyleConfig = this.defaultStyles.find(style => style.value === this.selectedStyle);
      this.emitBackgroundColor()
    },
    selectOption(key, value) {
      if (key === 'backgroundColor') {
        this.emitBackgroundColor();
      }
      if (key.endsWith('Probability')) {
        this.defaultStyles.find(style => style.value === this.selectedStyle)['config'][key] = value;
        return;
      } else if (this.defaultStyles.find(item => item.value === this.selectedStyle)['config'][key + 'Probability'] !== undefined) {
        this.defaultStyles.find(style => style.value === this.selectedStyle)['config'][key + 'Probability'] = 100;
      }
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
      this.currentOptions = {};
      this.resetStyleOptions();
      let originalOptions = this.getStyleOptions(styleValue);
      this.styleOptions = [...this.styleOptions, ...originalOptions];
      for (let i = 0; i < this.styleOptions.length; i++) {
        if (i <= 1) continue;
        let option = this.styleOptions[i];
        if (option.type === 'select') {
          this.currentOptions[option.name] = [option.default[Math.floor(Math.random() * option.default.length)].value];
        } else if (option.type === 'color') {
          this.currentOptions[option.name] = [option.default[Math.floor(Math.random() * option.default.length)].value];
          this.currentOptions[option.name + 'PickerVisible'] = false;
        } else {
          this.currentOptions[option.name] = option.default;
        }
      }
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
          convertedOptions[option.name + 'PickerVisible'] = false;
        } else {
          convertedOptions[option.name] = option.default;
        }
      }
      convertedOptions['size'] = 128;
      return {originalOptions, convertedOptions};
    },
    getStyleOptions(styleName) {
      try {
        // use dicebearCollection to get the style schema
        const style = dicebearCollection[styleName];
        if (!style || !style.schema || !style.schema.properties) {
          return [];
        }
        // combine core options with style-specific options
        const combinedProperties = {
          ...schema.properties,
          ...style.schema.properties
        };
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
        if (key === 'size' || key === 'randomizeIds' || key === 'backgroundRotation' || key === 'base') {
          continue;
        } else if (key.endsWith('Color')) {
          const uiOption = {
            name: key,
            type: 'color',
            default: defaultColors
          };
          uiOptions.push(uiOption);
          continue;
        } else if (key === 'seed' && (this.selectedStyle === 'initials')) {
          const uiOption = {
            name: key,
            type: 'text',
            default: config.default !== undefined ? config.default : ''
          };
          uiOptions.push(uiOption);
        } else if (key ==='radius'){
          const uiOption = {
            name: key,
            type: 'range',
            default: 5,
            min: config.minimum !== undefined ? config.minimum : 0,
            max: config.maximum !== undefined ? config.maximum : 100,
            step: config.multipleOf || 1
          };
          uiOptions.push(uiOption);
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
      return str.replace(/([a-z0-9])([A-Z])/g, '$1-$2').toLowerCase();
    },
    formatColor(color) {
      if (color === 'transparent') return 'transparent';
      if (color.startsWith('#')) {
        return color.substring(1);
      }
      if (/^[a-fA-F0-9]{6}$/.test(color)) {
        return color;
      }
      return 'ffffff';
    },
    formatOptionName(str) {
      if (!str) return '';
      const kebab = str.replace(/([a-z0-9])([A-Z])/g, '$1 $2');
      return kebab.split(' ').map(word =>
          word.charAt(0).toUpperCase() + word.slice(1)
      ).join(' ');
    },
    emitBackgroundColor() {
      if (this.selectedStyle && this.defaultStyles) {
        const styleConfig = this.defaultStyles.find(style => style.value === this.selectedStyle);
        if (styleConfig && styleConfig.config && styleConfig.config.backgroundColor) {
          this.$emit('update:background', styleConfig.config.backgroundColor[0]);
        }
      }
    },
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
    this.selectStyle();
    this.emitBackgroundColor();
  },
  beforeUnmount() {
    this.defaultStyles.forEach(style => {
      if (style.config) {
        Object.keys(style.config).forEach(key => {
          if (key.endsWith('EventHandler') && style.config[key]) {
            document.removeEventListener('click', style.config[key]);
          }
        });
      }
    });
  },
};
</script>

<style scoped>
.chessboard-bg {
  background-image: linear-gradient(45deg, #ccc 25%, transparent 25%),
  linear-gradient(-45deg, #ccc 25%, transparent 25%),
  linear-gradient(45deg, transparent 75%, #ccc 75%),
  linear-gradient(-45deg, transparent 75%, #ccc 75%);
  background-size: 20px 20px;
  background-position: 0 0, 0 10px, 10px -10px, -10px 0px;
  background-color: #eee;
  display: flex;
  align-items: center;
  justify-content: center;
}
</style>
