<template>
  <div class="fileViewBox">
    <el-tabs
      v-model="activeName"
      type="card"
      class="demo-tabs"
      @tab-click="handleClick"
    >
      <el-tab-pane label="原始文件" name="first">
        <el-tabs
          v-model="activeName1"
          class="demo-tabs1"
          @tab-click="handleClick1"
        >
          <el-tab-pane label="任务名称" name="first1">
            <ul class="taskInfoUl">
              <li>文件名称 xx</li>
              <li>文件大小 1M</li>
              <li>有效时长 20min</li>
              <li>总时长 1小时</li>
              <li>语种 中文</li>
              <li>是否转写 否</li>
              <li>是否降噪 否</li>
            </ul>
          </el-tab-pane>
        </el-tabs>
        <div class="fileBox">
          <div class="fileOriginal">
            <div class="fileOriginalTitle">原始文件</div>
            <div class="fileOriginalContent">
              <div class="videoBox">
                <div v-if="isVideoLoading" class="video-loading">
                  <el-icon class="loading-icon"><Loading /></el-icon>
                  <span>视频加载中...</span>
                </div>
                <div v-else-if="videoLoadError" class="video-error">
                  <el-icon class="error-icon"><CircleClose /></el-icon>
                  <span>视频加载失败</span>
                  <el-button type="primary" size="small" @click="retryLoadVideo">重试</el-button>
                </div>
                <video
                  ref="videoRef"
                  :src="videoUrl"
                  @loadstart="handleVideoLoadStart"
                  @loadeddata="handleVideoLoaded"
                  @error="handleVideoError"
                >
                  您的浏览器不支持 video 标签。
                </video>
              </div>
              <div class="audioTrack">
                音轨播放区
                 <!-- <AudioWave :audioUrl="audioUrl" :bindVideo="videoRef" /> -->
              </div>
              <div class="audioAction">
                <el-button type="success" plain @click="playVideo"
                  >▶ 播放</el-button
                >
                <!-- <button @click="playVideo">▶ 播放</button> -->
                <el-button type="danger" plain @click="pauseVideo"
                  >⏸ 暂停</el-button
                >
                <el-button type="primary" plain @click="increaseVolume"
                  >🔊 增大音量</el-button
                >
                <el-button color="#626aef" plain @click="decreaseVolume"
                  >🔉 减小音量</el-button
                >
                <!-- <p>当前音量：{{ (currentVolume * 100).toFixed(0) }}%</p> -->
              </div>
            </div>
          </div>
          <div class="transcriptionPreview">
            <div class="transcriptionPreviewTop">
              <div class="transcriptionPreviewTitle">转写预览</div>
              <div class="transcriptionPreviewDownload">
                <el-dropdown>
                  <span class="el-dropdown-link">
                    转写下载
                    <el-icon class="el-icon--right">
                      <arrow-down />
                    </el-icon>
                  </span>
                  <template #dropdown>
                    <el-dropdown-menu>
                      <el-dropdown-item>带时间戳文本</el-dropdown-item>
                      <el-dropdown-item>无时间戳文本</el-dropdown-item>
                      <el-dropdown-item>word文档</el-dropdown-item>
                    </el-dropdown-menu>
                  </template>
                </el-dropdown>
              </div>
            </div>
            <div class="transcriptionPreviewContent">转写预览区</div>
          </div>
        </div>
      </el-tab-pane>
      <el-tab-pane label="文件降噪" name="second">
        <el-tabs
          v-model="activeName2"
          class="demo-tabs2"
          @tab-click="handleClick2"
        >
          <el-tab-pane label="任务名称" name="first2">
            <ul class="taskInfoUl">
              <li>文件名称 xx</li>
              <li>文件大小 1M</li>
              <li>有效时长 20min</li>
              <li>总时长 1小时</li>
              <li>语种 中文</li>
              <li>是否转写 否</li>
              <li>是否降噪 否</li>
            </ul>
          </el-tab-pane>
        </el-tabs>
        <div class="fileBox2">
          <div class="fileReduction">
            <div class="fileReductionTop">
              <div class="fileReductionTitle">降噪文件</div>
              <div class="fileReductionDownload">
                <el-dropdown>
                  <span class="el-dropdown-link">
                    降噪文件下载
                    <el-icon class="el-icon--right">
                      <arrow-down />
                    </el-icon>
                  </span>
                  <template #dropdown>
                    <el-dropdown-menu>
                      <el-dropdown-item>带时间戳文本</el-dropdown-item>
                      <el-dropdown-item>无时间戳文本</el-dropdown-item>
                      <el-dropdown-item>word文档</el-dropdown-item>
                    </el-dropdown-menu>
                  </template>
                </el-dropdown>
              </div>
            </div>
            <div class="fileReductionContent">
              <div class="videoBox">
                <div v-if="isVideoLoading" class="video-loading">
                  <el-icon class="loading-icon"><Loading /></el-icon>
                  <span>视频加载中...</span>
                </div>
                <div v-else-if="videoLoadError" class="video-error">
                  <el-icon class="error-icon"><CircleClose /></el-icon>
                  <span>视频加载失败</span>
                  <el-button type="primary" size="small" @click="retryLoadVideo">重试</el-button>
                </div>
                <video
                  ref="videoRef1"
                  :src="videoUrl"
                  @loadstart="handleVideoLoadStart"
                  @loadeddata="handleVideoLoaded"
                  @error="handleVideoError"
                >
                  您的浏览器不支持 video 标签。
                </video>
              </div>
              <div class="audioTrack">音轨播放区</div>
              <div class="audioAction">
                <el-button type="success" plain @click="playVideo1"
                  >▶ 播放</el-button
                >
                <!-- <button @click="playVideo">▶ 播放</button> -->
                <el-button type="danger" plain @click="pauseVideo1"
                  >⏸ 暂停</el-button
                >
                <el-button type="primary" plain @click="increaseVolume1"
                  >🔊 增大音量</el-button
                >
                <el-button color="#626aef" plain @click="decreaseVolume1"
                  >🔉 减小音量</el-button
                >
                <!-- <p>当前音量：{{ (currentVolume * 100).toFixed(0) }}%</p> -->
              </div>
            </div>
          </div>
          <div class="transcriptionPreview">
            <div class="transcriptionPreviewTop">
              <div class="transcriptionPreviewTitle">转写预览</div>
              <div class="transcriptionPreviewDownload">
                <el-dropdown>
                  <span class="el-dropdown-link">
                    转写下载
                    <el-icon class="el-icon--right">
                      <arrow-down />
                    </el-icon>
                  </span>
                  <template #dropdown>
                    <el-dropdown-menu>
                      <el-dropdown-item>带时间戳文本</el-dropdown-item>
                      <el-dropdown-item>无时间戳文本</el-dropdown-item>
                      <el-dropdown-item>word文档</el-dropdown-item>
                    </el-dropdown-menu>
                  </template>
                </el-dropdown>
              </div>
            </div>
            <div class="transcriptionPreviewContent">转写预览区</div>
          </div>
        </div>
      </el-tab-pane>
      <el-tab-pane label="降噪转写" name="third">
        <el-tabs
          v-model="activeName3"
          class="demo-tabs3"
          @tab-click="handleClick3"
        >
          <el-tab-pane label="任务名称" name="first3">
            <ul class="taskInfoUl">
              <li>文件名称 xx</li>
              <li>文件大小 1M</li>
              <li>有效时长 20min</li>
              <li>总时长 1小时</li>
              <li>语种 中文</li>
              <li>是否转写 否</li>
              <li>是否降噪 否</li>
            </ul>
          </el-tab-pane>
        </el-tabs>
        <div class="fileBox3">
          <el-button type="primary">启动降噪转写</el-button>
          <div class="item">处理进度：</div>
          <div class="demo-progress">
            <el-progress :percentage="percentage" :status="getStatus" />
          </div>
        </div>
      </el-tab-pane>
    </el-tabs>
    <el-button
      v-for="button in buttons"
      :key="button.text"
      :type="button.type"
      class="returnBtn"
      text
      @click="router.push({ path: 'task-operation' ,query: { index:4 ,id:taskId} })"
    >
      {{ button.text }}
    </el-button>
  </div>
</template>

<script lang="ts" setup>
import { ref, computed, reactive, nextTick, onMounted, watch } from "vue";
import type { TabsPaneContext } from "element-plus";
import { ArrowDown, Loading, CircleClose } from "@element-plus/icons-vue";
import TableSearch from "@/components/operation-search.vue";
import { useRoute } from "vue-router";
import { useRouter } from "vue-router";
const router = useRouter();
const route = useRoute();
const id = ref<string>('');
const taskId = ref<string>('');
const fileInfo = ref(null);
const activeName = ref('first');
const activeName1 = ref('first1');
const activeName2 = ref('first2');
const activeName3 = ref('first3');
const percentage = ref(90);
const getStatus = computed(() => {
  if (percentage.value === 100) return "success";
  return ""; // 默认值
});

// 在组件挂载时获取并解析文件信息
onMounted(() => {
  const query = route.query;
  id.value = query.id as string;
  taskId.value = query.taskId as string;
  const index = query.index;
  
  // 设置默认标签页
  if (index == "1") {
    activeName.value = "first";
  } else if (index == "2") {
    activeName.value = "second";
  } else if (index == "3") {
    activeName.value = "third";
  } else {
    activeName.value = "first";
  }
  
  // 解析传递的文件信息
  if (query.fileInfo) {
    try {
      fileInfo.value = JSON.parse(query.fileInfo as string);
      console.log('fileView页面接收到的文件信息：', fileInfo.value);
      console.log('文件ID：', id.value);
      console.log('任务ID：', taskId.value);
      
      // 更新页面显示的文件信息
      if (fileInfo.value) {
        // 更新任务名称标签页中的信息
        const taskInfoItems = document.querySelectorAll('.taskInfoUl li');
        if (taskInfoItems.length > 0) {
          taskInfoItems[0].textContent = `文件名称 ${fileInfo.value.filename || '未知'}`;
          taskInfoItems[1].textContent = `文件大小 ${fileInfo.value.size || '未知'}`;
          taskInfoItems[2].textContent = `有效时长 ${fileInfo.value.effective_voice || '未知'}`;
          taskInfoItems[3].textContent = `总时长 ${fileInfo.value.total_voice || '未知'}`;
          taskInfoItems[4].textContent = `语种 ${fileInfo.value.language || '未知'}`;
          taskInfoItems[5].textContent = `是否转写 ${fileInfo.value.status === '已转写' ? '是' : '否'}`;
          taskInfoItems[6].textContent = `是否降噪 ${fileInfo.value.status === '已降噪' ? '是' : '否'}`;
        }
        videoUrl.value = fileInfo.value.url;
        isVideoLoading.value = true;
        videoLoadError.value = false;
      }
    } catch (error) {
      console.error('解析文件信息失败：', error);
    }
  }
});

const videoUrl = ref('../../public/vedio/2021届清华美院动画毕设 _《万华镜》——百年党庆，献礼中华五十六个民族.mp4')
const videoRef = ref(null);
const videoRef1 = ref(null);
const currentVolume = ref(1); // 音量范围是 0~1
const currentVolume1 = ref(1); // 音量范围是 0~1

const isVideoLoading = ref(true);
const videoLoadError = ref(false);

const handleVideoLoadStart = () => {
  isVideoLoading.value = true;
  videoLoadError.value = false;
};

const handleVideoLoaded = () => {
  isVideoLoading.value = false;
  videoLoadError.value = false;
};

const handleVideoError = () => {
  isVideoLoading.value = false;
  videoLoadError.value = true;
};

const retryLoadVideo = () => {
  if (videoRef.value) {
    isVideoLoading.value = true;
    videoLoadError.value = false;
    videoRef.value.load();
  }
};

const playVideo = () => {
  if (videoRef.value) {
    videoRef.value.play();
  }
};

const pauseVideo = () => {
  if (videoRef.value) {
    videoRef.value.pause();
  }
};

const increaseVolume = () => {
  if (videoRef.value) {
    currentVolume.value = Math.min(currentVolume.value + 0.1, 1);
    videoRef.value.volume = currentVolume.value;
  }
};

const decreaseVolume = () => {
  if (videoRef.value) {
    currentVolume.value = Math.max(currentVolume.value - 0.1, 0);
    videoRef.value.volume = currentVolume.value;
  }
};

const playVideo1 = () => {
  if (videoRef1.value) {
    videoRef1.value.play();
  }
};

const pauseVideo1 = () => {
  if (videoRef1.value) {
    videoRef1.value.pause();
  }
};

const increaseVolume1 = () => {
  if (videoRef1.value) {
    currentVolume1.value = Math.min(currentVolume1.value + 0.1, 1);
    videoRef1.value.volume = currentVolume1.value;
  }
};

const decreaseVolume1 = () => {
  if (videoRef1.value) {
    currentVolume1.value = Math.max(currentVolume1.value - 0.1, 0);
    videoRef1.value.volume = currentVolume1.value;
  }
};

const handleClick = (tab: TabsPaneContext, event: Event) => {
  nextTick(() => {
    (document.activeElement as HTMLElement | null)?.blur?.();
  });
};

const handleClick1 = (tab: TabsPaneContext, event: Event) => {
  nextTick(() => {
    (document.activeElement as HTMLElement | null)?.blur?.();
  });
};

const handleClick2 = (tab: TabsPaneContext, event: Event) => {
  nextTick(() => {
    (document.activeElement as HTMLElement | null)?.blur?.();
  });
};

const handleClick3 = (tab: TabsPaneContext, event: Event) => {
  nextTick(() => {
    (document.activeElement as HTMLElement | null)?.blur?.();
  });
};

const buttons = [{ type: "primary", text: "⬅ 返回任务操作" }] as const;
const dialogVisible = ref(false);
import { ElMessage, ElMessageBox } from "element-plus";
import type { UploadProps, UploadUserFile } from "element-plus";

const fileList = ref<UploadUserFile[]>([
  {
    name: "element-plus-logo.svg",
    url: "https://element-plus.org/images/element-plus-logo.svg",
  },
  {
    name: "element-plus-logo2.svg",
    url: "https://element-plus.org/images/element-plus-logo.svg",
  },
]);

const handleRemove: UploadProps["onRemove"] = (file, uploadFiles) => {
  console.log(file, uploadFiles);
};

const handlePreview: UploadProps["onPreview"] = (uploadFile) => {
  console.log(uploadFile);
};

const handleExceed: UploadProps["onExceed"] = (files, uploadFiles) => {
  ElMessage.warning(
    `The limit is 3, you selected ${files.length} files this time, add up to ${
      files.length + uploadFiles.length
    } totally`
  );
};

const beforeRemove: UploadProps["beforeRemove"] = (uploadFile, uploadFiles) => {
  return ElMessageBox.confirm(
    `Cancel the transfer of ${uploadFile.name} ?`
  ).then(
    () => true,
    () => false
  );
};
const detection = () => {
  // console.log("启动检测");
  activeName.value = "second";
};
const transcription = () => {
  // console.log("启动转写");
  activeName.value = "third";
};
</script>

<style scoped lang="scss">
.fileViewBox {
  position: relative;
  width: 100%;
  height: 100%;
}
.demo-tabs1,
.demo-tabs2,
.demo-tabs3,
.demo-tabs4 {
  width: 96%;
  margin: 0 auto;
  border: 1px solid #dcdfe6;
  padding: 20px;
  border-radius: 10px;
  box-sizing: border-box;
  
  :deep(.el-tabs__content) {
    padding: 0;
  }
}
.demo-tabs1 > .el-tabs__content {
  color: #6b778c;
  font-size: 32px;
  font-weight: 600;
}
.demo-tabs {
  box-sizing: border-box;
}
.demo-tabs > .el-tabs__content {
  padding: 20px;
  color: #6b778c;
  font-size: 32px;
  font-weight: 600;
}
.el-tabs {
  background-color: #fff;
  height: 100%;
}
::v-deep(.el-tabs__item.is-active) {
  border-bottom-color: #e4e7ed !important;
}
.returnBtn {
  position: absolute;
  right: 10px;
  top: 5px;
}
.taskInfoUl {
  list-style: none;
  display: flex;
  justify-content: space-between;
  padding: 0;
  margin: 0;
  width: 100%;
  
  li {
    flex: 1;
    text-align: center;
    padding: 0 10px;
    white-space: nowrap;
    overflow: hidden;
    text-overflow: ellipsis;
    max-width: 200px;
    color: #606266;
    font-size: 14px;
    
    &:not(:last-child) {
      border-right: 1px solid #EBEEF5;
    }
  }
}
.fileBox,
.fileBox2,
.fileBox3,
.fileBox4 {
  width: 96%;
  height: 600px;
  border-radius: 10px;
  box-sizing: border-box;
  border: 1px solid #dcdfe6;
  display: flex;
  padding: 20px;
  margin: 0 auto;
  margin-top: 20px;
  overflow-y: scroll;
  .fileOriginal {
    width: 96%;
    margin: 0 auto;
    // background-color: red;
    .fileOriginalTitle {
      font-weight: bolder;
      margin-bottom: 10px;
    }
    .fileOriginalContent {
      width: 90%;
      margin: 0 auto;
      // height: 300px;
      overflow-y: scroll;
      border: 1px solid #dcdfe6;
      border-radius: 10px;
      padding: 10px;
      box-sizing: border-box;
      .videoBox {
        width: 50%;
        margin: 0 auto;
        position: relative;
        min-height: 200px;
        display: flex;
        align-items: center;
        justify-content: center;
        
        video {
          width: 100%;
          height: 100%;
          object-fit: contain;
        }
        
        .video-loading,
        .video-error {
          position: absolute;
          top: 0;
          left: 0;
          width: 100%;
          height: 100%;
          display: flex;
          flex-direction: column;
          align-items: center;
          justify-content: center;
          background: rgba(255, 255, 255, 0.9);
          z-index: 1;
          
          .loading-icon {
            font-size: 32px;
            color: #409EFF;
            animation: rotating 2s linear infinite;
          }
          
          .error-icon {
            font-size: 32px;
            color: #F56C6C;
            margin-bottom: 10px;
          }
          
          span {
            margin: 10px 0;
            color: #606266;
          }
        }
      }
      .audioTrack {
        width: 50%;
        height: 50px;
        border: 1px solid #dcdfe6;
        border-radius: 10px;
        padding: 10px;
        box-sizing: border-box;
        margin: 10px auto;
      }
      .audioAction {
        width: 50%;
        margin: 0 auto;
        display: flex;
        justify-content: space-between;
      }
    }
  }
  .fileReduction {
    width: 96%;
    margin: 0 auto;
    // background-color: red;
    .fileReductionTop {
      display: flex;
      justify-content: space-between;
      font-weight: bolder;
      .fileReductionTitle {
        font-weight: bolder;
        margin-bottom: 10px;
      }
      .fileReductionDownload {
        color: #409eff !important;
        cursor: pointer;
        .example-showcase .el-dropdown-link {
          cursor: pointer;
          color: #409eff;
          display: flex;
          align-items: center;
        }
      }
    }

    .fileReductionContent {
      width: 90%;
      margin: 0 auto;
      // height: 300px;
      overflow-y: scroll;
      border: 1px solid #dcdfe6;
      border-radius: 10px;
      padding: 10px;
      box-sizing: border-box;
      .videoBox {
        width: 50%;
        margin: 0 auto;
        position: relative;
        min-height: 200px;
        display: flex;
        align-items: center;
        justify-content: center;
        
        video {
          width: 100%;
          height: 100%;
          object-fit: contain;
        }
        
        .video-loading,
        .video-error {
          position: absolute;
          top: 0;
          left: 0;
          width: 100%;
          height: 100%;
          display: flex;
          flex-direction: column;
          align-items: center;
          justify-content: center;
          background: rgba(255, 255, 255, 0.9);
          z-index: 1;
          
          .loading-icon {
            font-size: 32px;
            color: #409EFF;
            animation: rotating 2s linear infinite;
          }
          
          .error-icon {
            font-size: 32px;
            color: #F56C6C;
            margin-bottom: 10px;
          }
          
          span {
            margin: 10px 0;
            color: #606266;
          }
        }
      }
      .audioTrack {
        width: 50%;
        height: 50px;
        border: 1px solid #dcdfe6;
        border-radius: 10px;
        padding: 10px;
        box-sizing: border-box;
        margin: 10px auto;
      }
      .audioAction {
        width: 50%;
        margin: 0 auto;
        display: flex;
        justify-content: space-between;
      }
    }
  }
  .transcriptionPreview {
    width: 96%;
    margin: 20px auto;
    .transcriptionPreviewTop {
      display: flex;
      justify-content: space-between;
      font-weight: bolder;
      .transcriptionPreviewTitle {
        margin: 20px 0;
      }
      .transcriptionPreviewDownload {
        color: #409eff !important;
        cursor: pointer;
        .example-showcase .el-dropdown-link {
          cursor: pointer;
          color: #409eff;
          display: flex;
          align-items: center;
        }
      }
    }
    .transcriptionPreviewContent {
      width: 100%;
      border-radius: 10px;
      box-sizing: border-box;
      border: 1px solid #dcdfe6;
      padding: 10px;
      height: 600px;
    }
  }
}
.fileBox,
.fileBox2,
.fileBox3,
.fileBox4 {
  display: block;
  .item {
    margin: 20px 0;
  }
}
.fileBox3 {
  height: 180px;
}
::v-deep(.el-upload-list) {
  //  background-color: red;
  position: fixed;
  left: 11%;
  top: 35%;
  width: 49%;
}
.el-upload-list__item {
  margin: 10px 0;
}
.demo-progress .el-progress--line {
  margin-bottom: 15px;
  max-width: 600px;
}
.operationBottom {
  margin-top: 20px;
  display: flex;
  .btn {
    margin-right: 10px;
  }
}
.dialog-footer {
  position: absolute;
  bottom: 10px;
  right: 10px;
}

@keyframes rotating {
  from {
    transform: rotate(0deg);
  }
  to {
    transform: rotate(360deg);
  }
}
</style>
