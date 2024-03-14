<template>
  <div>
    <el-button :loading="loading" @click="captureScreenshot">截图</el-button>

    <div class="body">
      <el-table :data="tableData" border style="width: 100%" height="300px">
        <el-table-column prop="date" label="Date" width="180" />
        <el-table-column prop="name" label="Name" width="180" />
        <el-table-column prop="address" label="Address" />
      </el-table>
    </div>
  </div>

  <div class="capture-warp" v-if="imgUrl">
    <div class="capture-warp-body">
      <vueCropper
        ref="cropper"
        :img="imgUrl"
        :outputSize="option.outputSize"
        :outputType="option.outputType"
        :autoCrop="option.autoCrop"
        :canMove="option.canMove"
        :full="option.full"
        :centerBox="option.centerBox"
        :limitMinSize="0"
      ></vueCropper>
    </div>

    <div class="capture-warp-foot">
      <el-button type="primary" @click="toggleCrop">{{
        isStartCrop ? "停止截图" : "开始截图"
      }}</el-button>
      <el-button type="primary" @click="clearCrop">清除截图</el-button>
      <el-button type="primary" @click="cancel">取消</el-button>
      <el-button type="primary" @click="print">打印</el-button>
    </div>
  </div>
</template>

<script setup>
import { nextTick, reactive, ref } from "vue";
import html2canvas from "html2canvas";
import "vue-cropper/dist/index.css";
import { VueCropper } from "vue-cropper";
import printJS from "print-js";

const tableData = ref([
  {
    date: "2016-05-03",
    name: "Tom",
    address: "No. 189, Grove St, Los Angeles",
  },
  {
    date: "2016-05-02",
    name: "Tom",
    address: "No. 189, Grove St, Los Angeles",
  },
  {
    date: "2016-05-04",
    name: "Tom",
    address: "No. 189, Grove St, Los Angeles",
  },
  {
    date: "2016-05-01",
    name: "Tom",
    address: "No. 189, Grove St, Los Angeles",
  },
]);


const loading = ref(false);
const imgUrl = ref("");
const cropper = ref();
const captureScreenshot = async () => {
  loading.value = true;
  const cloneDom = document.body;
  const canvas = await html2canvas(cloneDom, {
    useCORS: true,
    logging: true,
    scale: 2,
    dpi: 100,
    foreignObjectRendering: true,
  });
  const screenshot = canvas.toDataURL("image/png");
  console.log(screenshot);
  imgUrl.value = screenshot;
  loading.value = false;
  nextTick(() => {
    toggleCrop();
  });
};

const option = ref({
  outputSize: 1,
  outputType: "png",
  autoCrop: true,
  canMove: true,
  infoTrue: true,
  full: true,
  centerBox: true,
});

const isStartCrop = ref(false);

const toggleCrop = () => {
  isStartCrop.value = !isStartCrop.value;
  if (isStartCrop.value) {
    cropper.value.startCrop();
  } else {
    cropper.value.stopCrop();
  }
};

const clearCrop = () => {
  cropper.value.clearCrop();
};

const cancel = () => {
  imgUrl.value = "";
};

const print = () => {
  cropper.value.getCropData((data) => {
    console.log(data);
    printJS({
      printable: data,
      type: "image",
    });
  });
};
</script>

<style>
.capture-warp {
  position: fixed;
  top: 0;
  left: 0;
  bottom: 0;
  right: 0;
  display: flex;
  flex-direction: column;
  width: 100%;
  height: 100%;
  z-index: 9999;
}

.capture-warp-body {
  flex: 1;
}

.capture-warp-foot {
  padding: 10px 0;
  background-color: #fff;
  text-align: center;
}
</style>
