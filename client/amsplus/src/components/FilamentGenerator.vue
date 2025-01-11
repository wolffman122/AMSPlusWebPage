<template>
    <div class="filament-generator">
      <h1 class="title">AMS Plus Filament Generator</h1>
  
      <!-- 卡片 -->
      <div class="card">
        <!-- 表单区 -->
        <div class="form-section">
          <!-- 1. 颜色 -->
          <div class="form-group">
            <label>Color</label>
            <input
              type="color"
              v-model="colorHex"
              @change="onParamChange"
              class="color-picker-large"
            />
            <p class="desc">
              <span class="hex">{{ colorHex.toUpperCase() }}</span>
            </p>
          </div>
  
          <!-- 2. 材质 -->
          <div class="form-group">
            <label>Material Type</label>
                <!-- 下拉选项 -->
                <select v-model="type" @change="onParamChange">
                <option
                    v-for="(m, index) in materialList"
                    :key="index"
                    :value="m.label"
                >
                    {{ m.label }}
                </option>
                </select>
            </div>
  
          <!-- 3. 最小温度 -->
          <div class="form-group">
            <label>Min Temp</label>
            <input
              type="number"
              v-model.number="minTemp"
              @change="onParamChange"
            />
          </div>
  
          <!-- 4. 最大温度 -->
          <div class="form-group">
            <label>Max Temp</label>
            <input
              type="number"
              v-model.number="maxTemp"
              @change="onParamChange"
            />
          </div>
  
          <!-- 5. Surface (A/B)：带图片的互斥“按钮” -->
          <div class="form-group surface-group">
            <label>Surface</label>
            <div class="radio-wrapper">
              <label
                :class="{'selected': surface === 'A'}"
              >
                <input
                  type="radio"
                  value="A"
                  v-model="surface"
                  @change="onParamChange"
                />
                <img
                  class="surface-img"
                  src="@/assets/surfaceA.png"
                  alt="Surface A"
                />
                
              </label>
              <label
                :class="{'selected': surface === 'B'}"
              >
                <input
                  type="radio"
                  value="B"
                  v-model="surface"
                  @change="onParamChange"
                />
                <img
                  class="surface-img"
                  src="@/assets/surfaceB.png"
                  alt="Surface B"
                />
                
              </label>
            </div>
          </div>
        </div>
  
        <!-- JSON 输出 (紧凑格式) -->
        <div class="output-section">
          <label>Generator JSON Output</label>
          <textarea
            class="json-output"
            v-model="generatedJson"
            readonly
          ></textarea>
        </div>
  
        <!-- 按钮 + 间隙 -->
        <div class="button-section">
          <button
            class="main-button"
            :class="showGenerateBtn ? 'generate' : 'copy'"
            @click="handleMainButtonClick"
          >
            {{ showGenerateBtn ? 'Generate' : 'Copy' }}
          </button>
        </div>
      </div>
  
      <!-- 轻提示 -->
      <div v-if="showToast" class="toast">
        已复制到剪贴板！
      </div>
    </div>
  </template>
  
  <script>
  export default {
    name: "FilamentGenerator",
    data() {
      return {
        colorHex: "#FFFF00",
        type: "PLA",
        minTemp: 220,
        maxTemp: 240,
        surface: "A",
  
        generatedJson: "",
        showGenerateBtn: true,
        showToast: false,
        // 你的材料列表： [{ label: 材质名称, code: 材质代码 }, ...]
        materialList: [
            { label: "TPU",            code: "GFU99" },
            { label: "PLA",            code: "GFL99" },
            { label: "PLA High Speed", code: "GFL95" },
            { label: "PLA Silk",       code: "GFL96" },
            { label: "PETG",           code: "GFG99" },
            { label: "PET-CF",         code: "GFG99" },
            { label: "ASA",            code: "GFB98" },
            { label: "ABS",            code: "GFB99" },
            { label: "PC",             code: "GFC99" },
            { label: "PA",             code: "GFN99" },
            { label: "PA-CF",          code: "GFN98" },
            { label: "PLA-CF",         code: "GFL98" },
            { label: "PVA",            code: "GFS99" },
            { label: "BVOH",           code: "GFS97" },
            { label: "EVA",            code: "GFR99" },
            { label: "HIPS",           code: "GFS98" },
            { label: "PCTG",           code: "GFG97" },
            { label: "PE",             code: "GFP99" },
            { label: "PE-CF",          code: "GFP98" },
            { label: "PHA",            code: "GFR98" },
            { label: "PP",             code: "GFP97" },
            { label: "PP-CF",          code: "GFP96" },
            { label: "PP-GF",          code: "GFP95" },
            { label: "PPA-CF",         code: "GFN97" },
            { label: "PPA-GF",         code: "GFN96" },
            { label: "Support",        code: "GFS00" }
        ]
      };
      
    },
    methods: {
      onParamChange() {
        this.showGenerateBtn = true;
        this.generatedJson = "";
      },
      handleMainButtonClick() {
        if (this.showGenerateBtn) {
          this.generateJson();
          this.showGenerateBtn = false;
        } else {
          this.fallbackCopyText(this.generatedJson);
          //this.copyJson();
        }
      },
      // 生成紧凑 JSON
      generateJson() {
        const payload = {
          protocol: "amsplus",
          version: "1.0",
          type: this.type,
          color_hex: this.colorHex.replace("#", ""),
          brand: "Generic",
          min_temp: String(this.minTemp),
          max_temp: String(this.maxTemp),
          surface: this.surface
        };
        const commandJson = {
          command: "filament",
          payload
        };
        this.generatedJson = JSON.stringify(commandJson);
      },
      // 复制
      copyJson() {
        navigator.clipboard
          .writeText(this.generatedJson)
          .then(() => {
            this.showToast = true;
            setTimeout(() => {
              this.showToast = false;
            }, 2000);
          })
          .catch(err => {
            console.error("复制失败: ", err);
          });
      },
      fallbackCopyText(text) {
        const textarea = document.createElement('textarea');
        textarea.value = text;
        // 让它绝对定位且移到视窗外
        textarea.style.position = 'fixed';
        textarea.style.top = '-9999px';
        document.body.appendChild(textarea);

        textarea.select();
        document.execCommand('copy');

        document.body.removeChild(textarea);
      }
    }
  };
  </script>
  
  <style scoped>
  /* 深色背景 */
  .filament-generator {
    display: flex;
    flex-direction: column;
    align-items: center;
    background-color: #1e1e1e;
    min-height: 100vh;
    padding: 12px;
    box-sizing: border-box;
    color: #ffffff;
    font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, Helvetica, Arial, sans-serif;
  }
  
  /* 标题 */
  .title {
    margin-bottom: 24px;
    font-weight: 600;
    font-size: 1.5rem;
    text-align: center;
    color: #f0f0f0;
  }
  
  /* 卡片：大屏可扩展到 800px */
  .card {
    background-color: #2c2c2c;
    border-radius: 8px;
    padding: 16px;
    box-shadow: 0 2px 8px rgba(0,0,0,0.5);
    width: 100%;
    max-width: 800px;
    margin: 0 auto;
    display: flex;
    flex-direction: column;
    gap: 16px;
  }
  
  /* 表单区：垂直排列 */
  .form-section {
    display: flex;
    flex-direction: column;
    gap: 16px;
  }
  
  /* form-group */
  .form-group {
    display: flex;
    flex-direction: column;
  }
  .form-group > label {
    margin-bottom: 8px;
    font-weight: 500;
    color: #ccc;
  }
  
  /* 说明文字 */
  .desc {
    margin-top: 8px;
    font-size: 0.9rem;
    color: #999;
  }
  .desc .hex {
    color: #fff;
    background-color: #444;
    padding: 2px 6px;
    border-radius: 4px;
  }
  
  /* 下拉框：加高 & 美化选项 */
  .form-group select {
    height: 48px;
    font-size: 1rem;
    border-radius: 6px;
    border: 1px solid #444;
    background-color: #3a3a3a;
    color: #fff;
    padding: 0 12px;
    outline: none;
    cursor: pointer;
  }
  
  /* 优化下拉框内部 option 的外观和高度 */
  .form-group select option {
    background-color: #3a3a3a;
    color: #fff;
    padding: 8px;       /* 选项上下内边距 */
    line-height: 2;   /* 行高更大 */
  }
  
  /* 数字输入框 (Min/Max Temp) */
  .form-group input[type="number"] {
    height: 48px;
    font-size: 1rem;
    border-radius: 6px;
    border: 1px solid #444;
    background-color: #3a3a3a;
    color: #fff;
    padding: 0 12px;
    outline: none;
  }
  
  /* 颜色选择器，大按钮 */
  .color-picker-large {
    width: 100%;
    height: 60px;
    border-radius: 6px;
    border: 2px solid #444;
    background: none;
    cursor: pointer;
    outline: none;
  }
  
  /* Surface 互斥按钮 (A/B) */
  .surface-group {
    display: flex;
    flex-direction: column;
    gap: 8px;
  }
  .radio-wrapper {
    display: flex;
    gap: 16px;
  }
  .radio-wrapper label {
    flex: 1 1 auto;    /* 让 label 自动伸缩，或指定一个最小宽度 */
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    background-color: #3a3a3a;
    border: 2px solid transparent;
    border-radius: 8px;
    cursor: pointer;
    padding: 8px;
    transition: border-color 0.2s;
    box-sizing: border-box;

  /* 让内容在小屏幕时也能压缩 */
    min-width: 0;  
  }
  .radio-wrapper input[type="radio"] {
    display: none;
  }
  .surface-img {
    width: auto;
    height: auto;
    max-width: 80px;  /* 最大宽度，可自行调节 */
    max-height: 80px; /* 最大高度，可自行调节 */
    object-fit: cover;
    margin-bottom: 4px;
  }
  .radio-wrapper label.selected {
    border-color: #2196f3;
  }
  
  /* JSON 输出 */
  .output-section {
    display: flex;
    flex-direction: column;
  }
  .output-section > label {
    margin-bottom: 8px;
    font-weight: 500;
    color: #ccc;
  }
  .json-output {
    height: 100px;
    resize: none;
    background-color: #3a3a3a;
    color: #fff;
    border: 1px solid #555;
    border-radius: 4px;
    padding: 8px;
    font-family: monospace;
  }
  
  /* 按钮区域：增加 margin-top */
  .button-section {
    display: flex;
    justify-content: center;
    /* 在此处加一点 margin-top，或可放在 .output-section 末尾加 margin-bottom */
    margin-top: 12px; 
  }
  
  .main-button {
    width: 140px;
    height: 44px;
    border: none;
    border-radius: 22px;
    font-size: 1rem;
    font-weight: 600;
    cursor: pointer;
    transition: background 0.3s;
    color: #ffffff;
  }
  
  /* Generate (绿色渐变) */
  .generate {
    background: linear-gradient(45deg, #4caf50, #2e7d32);
  }
  /* Copy (蓝色渐变) */
  .copy {
    background: linear-gradient(45deg, #2196f3, #1565c0);
  }
  .main-button:hover {
    filter: brightness(1.1);
  }
  
  /* 轻提示 */
  .toast {
    margin-top: 16px;
    background-color: #444;
    color: #fff;
    padding: 8px 16px;
    border-radius: 4px;
    animation: fadeInOut 2s forwards;
    text-align: center;
  }
  
  @keyframes fadeInOut {
    0% { opacity: 0; }
    10% { opacity: 1; }
    90% { opacity: 1; }
    100% { opacity: 0; }
  }
  
  /* 移动端自适应 */
  @media (max-width: 480px) {
    .card {
      width: 100%;
    }
  }
  </style>
  