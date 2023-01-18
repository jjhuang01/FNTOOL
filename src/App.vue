<template>
  <div class="m-auto contents mx-auto w-screen-lg">
    <el-card>
      <el-radio-group v-model="radio" @change="change">
        <el-radio value="1" label="1">生成数组</el-radio>
        <el-radio value="2" label="2">生成list</el-radio>
        <el-radio value="3" label="3">合成json</el-radio>
        <el-radio value="4" label="4">生成json</el-radio>
      </el-radio-group>
    </el-card>

    <div>
      <el-form label-position="top" :inline="true" class="grid grid-cols-3 gap-2 m-5">
        <el-form-item label="label">
          <textarea  @blur="submit" @input="submit" class="outline-gray-500 bg-zinc-100 col-span-1" ring v-model="data.label" name="" id="" cols="30"
            rows="10" placeholder="label">
              </textarea>
        </el-form-item>
        <el-form-item label="value" v-show="radio == 3">
          <textarea @blur="submit" @input="submit" class="outline-gray-500 bg-zinc-100 col-span-1" ring name="" v-model="data.value" id="" cols="30"
            rows="10" placeholder="value"></textarea>
        </el-form-item>
        <el-form-item label="结果(点击复制)" class="cursor-pointer" @click="copyHandle">
          <pre class="bg-zinc-100 p-4">
{{ (preData) }}
         </pre>
        </el-form-item>
      </el-form>
    </div>

    <!-- <div>
      <button class="basis-full bg-blue-500 block w-screen-sm mx-auto text-neutral-50 py-4 rounded-lg cursor-pointer"
        type="primary" size="large" @click="submit">确定</button>
    </div> -->
  </div>
</template>
<script setup lang="ts">
import { ref, reactive,getCurrentInstance } from 'vue'
import {ElMessage} from 'element-plus'
const { appContext } = getCurrentInstance()!
let radio = ref('1')
let data = reactive({
  label: [],
  value: [],
  h: []
})
let preData = ref(null)
function submit() {
  let _type = radio.value
  if (_type == 1) {
    var reg = /\n(\n)*( )*(\n)*\n/g
    let label = data.label.trim()
    preData.value = label.split(/[(\r\n\s)\r\n\s]+/)
  } else if (_type == 2) {
    var reg = /\n(\n)*( )*(\n)*\n/g
    let label = data.label.trim()
    let res = label.split(/[(\r\n\s)\r\n\s]+/)

    // preData.value = js_beautify(res.join("','")+"''")
    preData.value = "'" + res.join("','") + "'"
    // res = res.toString()
  } else if (_type == 3) {
    var reg = /\n(\n)*( )*(\n)*\n/g
    let label = data.label.trim().split(/[(\r\n\s)\r\n\s]+/)
    let _value = data.value.trim().split(/[(\r\n\s)\r\n\s]+/)
    let tmp = []

    label.forEach((element, idx) => {
      let map = {}
      map['label'] = element
      map['value'] = _value[idx]
      tmp.push(map)
    })
    preData.value = tmp

  } else if (_type == 4) {
    let label = data.label.trim()
    let res = label.split(/[(\r\n\s)\r\n\s]+/)

    let map = {}
    res.forEach((item, i) => {
      map[item] = ''
    })
    preData.value = map
  }
  // var reg = /\n(\n)*( )*(\n)*\n/g;
  // let label = data.label.trim()
  // label.replace(reg, "\n");
  // label.split(/[(\r\n\s)\r\n\s]+/)
  // console.log(label.split(/[(\r\n\s)\r\n\s]+/))
}

function change(e) {
  radio.value = e
  submit()
}

function copyHandle() {
  var text = preData.value
  if (navigator.clipboard) {
    // clipboard api 复制
    navigator.clipboard.writeText(text);
    ElMessage({
      type:'success',
      message:'复制成功'
    },appContext)
  } else {
    var textarea = document.createElement('textarea');
    document.body.appendChild(textarea);
    // 隐藏此输入框
    textarea.style.position = 'fixed';
    textarea.style.clip = 'rect(0 0 0 0)';
    textarea.style.top = '10px';
    // 赋值
    textarea.value = text;
    // 选中
    textarea.select();
    // 复制
    document.execCommand('copy', true);
    // 移除输入框
    document.body.removeChild(textarea);
    ElMessage({
      type:'success',
      message:'复制成功'
    },appContext)

  }

}
</script>
