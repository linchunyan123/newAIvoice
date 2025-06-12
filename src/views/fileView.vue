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
              <li>文件名称 {{ fileInfo?.filename || '未知' }}</li>
              <li>文件大小 {{ fileInfo?.size || '未知' }}</li>
              <li>有效时长 {{ fileInfo?.effective_voice || '未知' }}</li>
              <li>总时长 {{ fileInfo?.total_voice || '未知' }}</li>
              <li>语种 {{ fileInfo?.language || '未知' }}</li>
              <li>是否转写 {{ fileInfo?.status === '已转写' ? '是' : '否' }}</li>
              <li>是否降噪 {{ fileInfo?.status === '已降噪' ? '是' : '否' }}</li>
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
                  <span>媒体加载中...</span>
                </div>
                <div v-else-if="videoLoadError" class="video-error">
                  <el-icon class="error-icon"><CircleClose /></el-icon>
                  <span>媒体加载失败</span>
                  <el-button type="primary" size="small" @click="retryLoadVideo">重试</el-button>
                </div>
                <!-- 视频播放器 -->
                <video
                  v-if="isVideoFile"
                  ref="videoRef"
                  :src="videoUrl"
                  @loadstart="handleVideoLoadStart"
                  @loadeddata="handleVideoLoaded"
                  @error="handleVideoError"
                  controls
                >
                  您的浏览器不支持 video 标签。
                </video>
                <!-- 音频播放器 -->
                <audio
                  v-else
                  ref="audioRef"
                  :src="videoUrl"
                  @loadstart="handleVideoLoadStart"
                  @loadeddata="handleVideoLoaded"
                  @error="handleVideoError"
                  controls
                >
                  您的浏览器不支持 audio 标签。
                </audio>
              </div>
              <div class="audioTrack" ref="wavesurferContainer">
                <!-- WaveSurfer 将在这里渲染波形 -->
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
              <li>文件名称 {{ fileInfo?.filename || '未知' }}</li>
              <li>文件大小 {{ fileInfo?.size || '未知' }}</li>
              <li>有效时长 {{ fileInfo?.effective_voice || '未知' }}</li>
              <li>总时长 {{ fileInfo?.total_voice || '未知' }}</li>
              <li>语种 {{ fileInfo?.language || '未知' }}</li>
              <li>是否转写 {{ fileInfo?.status === '已转写' ? '是' : '否' }}</li>
              <li>是否降噪 {{ fileInfo?.status === '已降噪' ? '是' : '否' }}</li>
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
                <div v-if="isVideoLoading1" class="video-loading">
                  <el-icon class="loading-icon"><Loading /></el-icon>
                  <span>视频加载中...</span>
                </div>
                <div v-else-if="videoLoadError1" class="video-error">
                  <el-icon class="error-icon"><CircleClose /></el-icon>
                  <span>视频加载失败</span>
                  <el-button type="primary" size="small" @click="retryLoadVideo1">重试</el-button>
                </div>
                <video
                  ref="videoRef1"
                  :src="videoUrl"
                  @loadstart="handleVideoLoadStart1"
                  @loadeddata="handleVideoLoaded1"
                  @error="handleVideoError1"
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
              <li>文件名称 {{ fileInfo?.filename || '未知' }}</li>
              <li>文件大小 {{ fileInfo?.size || '未知' }}</li>
              <li>有效时长 {{ fileInfo?.effective_voice || '未知' }}</li>
              <li>总时长 {{ fileInfo?.total_voice || '未知' }}</li>
              <li>语种 {{ fileInfo?.language || '未知' }}</li>
              <li>是否转写 {{ fileInfo?.status === '已转写' ? '是' : '否' }}</li>
              <li>是否降噪 {{ fileInfo?.status === '已降噪' ? '是' : '否' }}</li>
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
import { ref, computed, reactive, nextTick, onMounted, watch, onBeforeUnmount } from "vue";
import type { TabsPaneContext } from "element-plus";
import { ArrowDown, Loading, CircleClose } from "@element-plus/icons-vue";
import TableSearch from "@/components/operation-search.vue";
import { useRoute } from "vue-router";
import { useRouter } from "vue-router";
import WaveSurfer from 'wavesurfer.js';
import { ElMessage, ElMessageBox } from "element-plus";
import type { UploadProps, UploadUserFile } from "element-plus";
import { ElLoading } from "element-plus";

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

// WaveSurfer 相关
const wavesurfer = ref(null);
const wavesurferContainer = ref(null);

// 初始化 WaveSurfer
const initWaveSurfer = () => {
  if (wavesurfer.value) {
    wavesurfer.value.destroy();
  }

  // 显示加载状态
  const loading = ElLoading.service({
    target: wavesurferContainer.value,
    text: '音轨加载中...',
    background: 'rgba(255, 255, 255, 0.9)',
    customClass: 'wave-loading'
  });

  wavesurfer.value = WaveSurfer.create({
    container: wavesurferContainer.value,
    waveColor: '#409EFF',
    progressColor: '#67C23A',
    cursorColor: '#409EFF',
    barWidth: 2,
    barRadius: 3,
    cursorWidth: 1,
    height: 60,
    barGap: 3,
    normalize: true,
    backend: 'WebAudio'
  });

  // 加载音频
  if (fileInfo.value?.voice_url) {
    wavesurfer.value.load(fileInfo.value.voice_url);
  }

  // 监听加载完成事件
  wavesurfer.value.on('ready', () => {
    loading.close();
    // 设置初始音量
    wavesurfer.value.setVolume(currentVolume.value);
    // console.log('音轨加载完成，总时长：', wavesurfer.value.getDuration());
  });

  // 监听加载错误事件
  wavesurfer.value.on('error', () => {
    loading.close();
    ElMessage.error('音轨加载失败');
  });

  // 同步视频/音频播放
  if (isVideoFile.value && videoRef.value) {
    // 视频播放时同步音轨
    videoRef.value.addEventListener('play', () => {
      // console.log('视频开始播放，当前时间：', videoRef.value.currentTime);
      wavesurfer.value?.play();
    });
    
    // 视频暂停时同步音轨
    videoRef.value.addEventListener('pause', () => {
      // console.log('视频暂停，当前时间：', videoRef.value.currentTime);
      wavesurfer.value?.pause();
    });
    
    // 视频进度变化时同步音轨
    videoRef.value.addEventListener('timeupdate', () => {
      if (videoRef.value && wavesurfer.value) {
        const progress = videoRef.value.currentTime / videoRef.value.duration;
        // console.log('视频进度更新，当前时间：', videoRef.value.currentTime, '总时长：', videoRef.value.duration, '进度：', progress);
        wavesurfer.value.seekTo(progress);
      }
    });

    // 音轨点击时同步视频
    wavesurfer.value.on('seek', (progress) => {
      if (videoRef.value) {
        const seekTime = videoRef.value.duration * progress;
        // console.log('音轨点击，目标时间：', seekTime, '总时长：', videoRef.value.duration, '进度：', progress);
        videoRef.value.currentTime = seekTime;
        // 如果视频当前是播放状态，则继续播放
        if (!videoRef.value.paused) {
          videoRef.value.play();
        }
      }
    });

    // 音轨点击事件
    wavesurfer.value.on('click', (progress) => {
      if (videoRef.value) {
        const seekTime = videoRef.value.duration * progress;
        // console.log('音轨点击，目标时间：', seekTime, '总时长：', videoRef.value.duration, '进度：', progress);
        videoRef.value.currentTime = seekTime;
        // 如果视频当前是播放状态，则继续播放
        if (!videoRef.value.paused) {
          videoRef.value.play();
        }
      }
    });
  } else if (!isVideoFile.value && audioRef.value) {
    // 音频播放时同步音轨
    audioRef.value.addEventListener('play', () => {
      // console.log('音频开始播放，当前时间：', audioRef.value.currentTime);
      wavesurfer.value?.play();
    });
    
    // 音频暂停时同步音轨
    audioRef.value.addEventListener('pause', () => {
      // console.log('音频暂停，当前时间：', audioRef.value.currentTime);
      wavesurfer.value?.pause();
    });
    
    // 音频进度变化时同步音轨
    audioRef.value.addEventListener('timeupdate', () => {
      if (audioRef.value && wavesurfer.value) {
        const progress = audioRef.value.currentTime / audioRef.value.duration;
        // console.log('音频进度更新，当前时间：', audioRef.value.currentTime, '总时长：', audioRef.value.duration, '进度：', progress);
        wavesurfer.value.seekTo(progress);
      }
    });

    // 音轨点击时同步音频
    wavesurfer.value.on('seek', (progress) => {
      if (audioRef.value) {
        const seekTime = audioRef.value.duration * progress;
        // console.log('音轨点击，目标时间：', seekTime, '总时长：', audioRef.value.duration, '进度：', progress);
        audioRef.value.currentTime = seekTime;
        // 如果音频当前是播放状态，则继续播放
        if (!audioRef.value.paused) {
          audioRef.value.play();
        }
      }
    });

    // 音轨点击事件
    wavesurfer.value.on('click', (progress) => {
      if (audioRef.value) {
        const seekTime = audioRef.value.duration * progress;
        // console.log('音轨点击，目标时间：', seekTime, '总时长：', audioRef.value.duration, '进度：', progress);
        audioRef.value.currentTime = seekTime;
        // 如果音频当前是播放状态，则继续播放
        if (!audioRef.value.paused) {
          audioRef.value.play();
        }
      }
    });
  }

  // 音轨播放时同步视频/音频
  wavesurfer.value.on('play', () => {
    if (isVideoFile.value && videoRef.value) {
      // console.log('音轨开始播放，视频当前时间：', videoRef.value.currentTime);
      videoRef.value.play();
    } else if (!isVideoFile.value && audioRef.value) {
      // console.log('音轨开始播放，音频当前时间：', audioRef.value.currentTime);
      audioRef.value.play();
    }
  });

  // 音轨暂停时同步视频/音频
  wavesurfer.value.on('pause', () => {
    if (isVideoFile.value && videoRef.value) {
      // console.log('音轨暂停，视频当前时间：', videoRef.value.currentTime);
      videoRef.value.pause();
    } else if (!isVideoFile.value && audioRef.value) {
      // console.log('音轨暂停，音频当前时间：', audioRef.value.currentTime);
      audioRef.value.pause();
    }
  });
};

const isVideoFile = ref(false);
const audioRef = ref(null);

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
      // console.log('fileView页面接收到的文件信息：', fileInfo.value);
      // console.log('文件ID：', id.value);
      // console.log('任务ID：', taskId.value);
      
      // 更新视频URL并判断文件类型
      if (fileInfo.value && fileInfo.value.url) {
        videoUrl.value = fileInfo.value.url;
        // 根据文件扩展名判断是否为视频文件
        const fileExtension = fileInfo.value.filename?.split('.').pop()?.toLowerCase();
        isVideoFile.value = ['mp4', 'webm', 'ogg', 'mov'].includes(fileExtension || '');
        isVideoLoading.value = true;
        isVideoLoading1.value = true;
        videoLoadError.value = false;
        videoLoadError1.value = false;

        // 初始化 WaveSurfer
        nextTick(() => {
          initWaveSurfer();
        });
      }
    } catch (error) {
      console.error('解析文件信息失败：', error);
    }
  }
});

const videoUrl = ref('')
const videoRef = ref(null);
const videoRef1 = ref(null);
const currentVolume = ref(1); // 音量范围是 0~1
const currentVolume1 = ref(1); // 音量范围是 0~1

const isVideoLoading = ref(true);
const videoLoadError = ref(false);

const isVideoLoading1 = ref(true);
const videoLoadError1 = ref(false);

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

const handleVideoLoadStart1 = () => {
  isVideoLoading1.value = true;
  videoLoadError1.value = false;
};

const handleVideoLoaded1 = () => {
  isVideoLoading1.value = false;
  videoLoadError1.value = false;
};

const handleVideoError1 = () => {
  isVideoLoading1.value = false;
  videoLoadError1.value = true;
};

const retryLoadVideo = () => {
  if (isVideoFile.value && videoRef.value) {
    isVideoLoading.value = true;
    videoLoadError.value = false;
    videoRef.value.load();
  } else if (!isVideoFile.value && audioRef.value) {
    isVideoLoading.value = true;
    videoLoadError.value = false;
    audioRef.value.load();
  }
};

const retryLoadVideo1 = () => {
  if (videoRef1.value) {
    isVideoLoading1.value = true;
    videoLoadError1.value = false;
    videoRef1.value.load();
  }
};

const playVideo = () => {
  if (isVideoFile.value && videoRef.value) {
    // console.log('点击播放按钮，视频当前时间：', videoRef.value.currentTime);
    videoRef.value.play();
  } else if (!isVideoFile.value && audioRef.value) {
    // console.log('点击播放按钮，音频当前时间：', audioRef.value.currentTime);
    audioRef.value.play();
  }
};

const pauseVideo = () => {
  if (isVideoFile.value && videoRef.value) {
    // console.log('点击暂停按钮，视频当前时间：', videoRef.value.currentTime);
    videoRef.value.pause();
  } else if (!isVideoFile.value && audioRef.value) {
    // console.log('点击暂停按钮，音频当前时间：', audioRef.value.currentTime);
    audioRef.value.pause();
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

const increaseVolume = () => {
  if (isVideoFile.value && videoRef.value) {
    currentVolume.value = Math.min(currentVolume.value + 0.1, 1);
    videoRef.value.volume = currentVolume.value;
    wavesurfer.value?.setVolume(currentVolume.value);
  } else if (!isVideoFile.value && audioRef.value) {
    currentVolume.value = Math.min(currentVolume.value + 0.1, 1);
    audioRef.value.volume = currentVolume.value;
    wavesurfer.value?.setVolume(currentVolume.value);
  }
};

const decreaseVolume = () => {
  if (isVideoFile.value && videoRef.value) {
    currentVolume.value = Math.max(currentVolume.value - 0.1, 0);
    videoRef.value.volume = currentVolume.value;
    wavesurfer.value?.setVolume(currentVolume.value);
  } else if (!isVideoFile.value && audioRef.value) {
    currentVolume.value = Math.max(currentVolume.value - 0.1, 0);
    audioRef.value.volume = currentVolume.value;
    wavesurfer.value?.setVolume(currentVolume.value);
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

// 在组件卸载前清理事件监听
onBeforeUnmount(() => {
  if (wavesurfer.value) {
    wavesurfer.value.destroy();
  }
  if (videoRef.value) {
    videoRef.value.removeEventListener('play', () => {});
    videoRef.value.removeEventListener('pause', () => {});
    videoRef.value.removeEventListener('timeupdate', () => {});
  }
});
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
        height: 80px;
        border: 1px solid #dcdfe6;
        border-radius: 10px;
        padding: 10px;
        box-sizing: border-box;
        margin: 10px auto;
        background: #fff;
        position: relative;
        overflow: hidden;

        :deep(.wave-loading) {
          .el-loading-spinner {
            top: 50%;
            margin-top: -15px;
          }
          
          .el-loading-text {
            font-size: 14px;
            margin-top: 5px;
          }
        }

        :deep(.wavesurfer-container) {
          position: relative;
          width: 100%;
          height: 100%;
          
          wave {
            width: 100% !important;
            height: 60px !important;
          }
        }
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
