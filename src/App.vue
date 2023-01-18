<template>
  <div class="container mx-auto w-screen-lg">
    <div>
      <el-card>
        <div class="flex">
          <div class="flex-1 flex-auto">
            <el-radio-group v-model="radio" @change="change">
              <el-radio value="1" label="1">生成数组</el-radio>
              <el-radio value="2" label="2">生成list</el-radio>
              <el-radio value="3" label="3">合成json</el-radio>
              <el-radio value="4" label="4">生成json</el-radio>
            </el-radio-group>
          </div>
          <a class="text-red-500" href="https://web.baimiaoapp.com/" target="_blank"
            >白描（截图识别文字）</a
          >
        </div>
      </el-card>
    </div>

    <div :class="['grid gap-10 py-10',{'grid-cols-3':radio==3?true:false},{'grid-cols-2':radio!=3?true:false}]">
      <div class="cursor-pointer">
        <h2 class="text-blue-500 flex py-3 bg-blue-100 indent-1em">
          <span class="flex-1">label</span>
          <button class="flex-1 text-right pr-1em" @click="zt(1)">粘贴</button>
        </h2>
        <textarea
          id="pasteInput"
          ref="textarea_label"
          @blur="submit"
          @input="submit"
          class="outline-gray-500 bg-zinc-100 col-span-1 h-500px w-full text-base"
          ring
          v-model="data.label"
          placeholder="label"
        >
        </textarea>
      </div>
      <div class="cursor-pointer" v-show="radio == 3">
        <h2 class="text-blue-500 flex py-3 bg-blue-100 indent-1em">
          <span class="flex-1">value</span>
          <button class="flex-1 text-right pr-1em" @click="zt(2)">粘贴</button>
        </h2>
        <textarea
          @blur="submit"
          @input="submit"
          class="outline-gray-500 bg-zinc-100 col-span-1 h-500px w-full text-base"
          ring
          name=""
          v-model="data.value"
          id=""
          placeholder="value"
        ></textarea>
      </div>
      <div class="cursor-pointer" @click="copyHandle">
        <h2 class="text-fuchsia-500 bg-fuchsia-100 py-3 indent-1em">复制</h2>
        <pre class="bg-zinc-100 p-4 h-500px text-xs" style="overflow-y: auto">
<code>
  {{ (preData) }}
</code>
         </pre>
      </div>
    </div>
  </div>
</template>
<script setup lang="ts">
import { ref, reactive, getCurrentInstance, onMounted } from "vue";
import { ElMessage } from "element-plus";
const { appContext } = getCurrentInstance()!;
const that = getCurrentInstance();

let radio = ref("1");
let data = reactive({
  label: [],
  value: [],
  h: [],
});
let preData = ref(null);
function submit() {
  let _type = radio.value;
  if (_type == 1) {
    var reg = /\n(\n)*( )*(\n)*\n/g;
    let label = data.label.trim();
    preData.value = label.split(/[(\r\n\s)\r\n\s]+/);
  } else if (_type == 2) {
    var reg = /\n(\n)*( )*(\n)*\n/g;
    let label = data.label.trim();
    let res = label.split(/[(\r\n\s)\r\n\s]+/);

    // preData.value = js_beautify(res.join("','")+"''")
    preData.value = "'" + res.join("','") + "'";
    // res = res.toString()
  } else if (_type == 3) {
    var reg = /\n(\n)*( )*(\n)*\n/g;
    let label = data.label.trim().split(/[(\r\n\s)\r\n\s]+/);
    let _value = data.value.trim().split(/[(\r\n\s)\r\n\s]+/);
    let tmp = [];

    label.forEach((element, idx) => {
      let map = {};
      map["label"] = element;
      map["value"] = _value[idx];
      tmp.push(map);
    });
    preData.value = tmp;
  } else if (_type == 4) {
    let label = data.label.trim();
    let res = label.split(/[(\r\n\s)\r\n\s]+/);

    let map = {};
    res.forEach((item, i) => {
      map[item] = "";
    });
    preData.value = map;
  }
  // var reg = /\n(\n)*( )*(\n)*\n/g;
  // let label = data.label.trim()
  // label.replace(reg, "\n");
  // label.split(/[(\r\n\s)\r\n\s]+/)
  // console.log(label.split(/[(\r\n\s)\r\n\s]+/))
}

function change(e) {
  radio.value = e;
  submit();
}

function copyHandle() {
  var text = preData.value;
  text = JSON.stringify(text);
  if (navigator.clipboard) {
    // clipboard api 复制
    navigator.clipboard.writeText(text);
    ElMessage(
      {
        type: "success",
        message: "复制成功",
      },
      appContext
    );
  } else {
    var textarea = document.createElement("textarea");
    document.body.appendChild(textarea);
    // 隐藏此输入框
    textarea.style.position = "fixed";
    textarea.style.clip = "rect(0 0 0 0)";
    textarea.style.top = "10px";
    // 赋值
    textarea.value = text;
    // 选中
    textarea.select();
    // 复制
    document.execCommand("copy", true);
    // 移除输入框
    document.body.removeChild(textarea);
    ElMessage(
      {
        type: "success",
        message: "复制成功",
      },
      appContext
    );
  }
}
async function zt(source) {
  const text = await navigator.clipboard.readText();
  source == 1 ? (data.label = text) : (data.value = text);
  submit();
}
</script>
