<template>
  <!-- <header class="header">
    <img src="https://cdn.jsdelivr.net/gh/fonghehe/picture/vue-h5-template/logo.png" /><span> {{ $t('title') }}</span>
  </header>
  <div class="intro-header">
    <div>{{ $t('introduction') }}</div>
    <a href="https://github.com/sunniejs/vue-h5-template.git">
      <nut-icon name="github" />
    </a>
  </div> -->
  <!-- <nut-cell-group :title="$t('home.support')" class="supportList">
    <nut-cell v-for="(item, index) in cellList" :key="index" :title="item" icon="Check" />
  </nut-cell-group>
  <nut-cell-group :title="$t('home.cssMultiLanguage')" class="supportList">
    <nut-cell>
      <div :class="['btn-confirm', locale]"></div>
    </nut-cell>
  </nut-cell-group> -->
  <!-- <div class="btn-wrap">
    <nut-button shape="square" size="small" type="default" @click="changeLang('zh-cn')">
      {{ $t('language.zh') }}
    </nut-button>
    <nut-button shape="square" size="small" type="default" @click="changeLang('en-us')">
      {{ $t('language.en') }}
    </nut-button>
  </div>
  {{ getUserInfo }} -->
  <div>
     <qr-capture @decode="onDecode" class="mb"> //图片上传二维码识别
	    <div style="color: red;" class="frame"></div>
	  </qr-capture>
   <div style="margin-top:30px;">
     <!-- {{(fileList[0] || {}).url}} -->
     <van-uploader :after-read="afterRead" />
   </div>
    <div style="margin-top:30px;">
      {{qrCodeInfo}}
      <van-button type="default" v-if="qrCodeInfo" @click="copy" size="small" >复制</van-button>
      <!-- <nut-button size="small" @click="copy" type="default">复制</nut-button> -->
    </div>
    <!-- <nut-uploader method="put" ref="uploadRef" v-model:file-list="fileList" :before-xhr-upload="beforeXhrUpload" ></nut-uploader> -->
  </div>
</template>

<script lang="ts" setup name="HomePage">
import { ref, createApp, getCurrentInstance } from 'vue';
import { QrCapture } from 'vue3-qr-reader'
import useClipboard from 'vue-clipboard3';
import QrCode from 'qrcode-decoder'
const { toClipboard } = useClipboard();
createApp({
  components:{
    QrCapture
  }
})
// 二维码解码 
// 传入file对象
function getQrUrl (file) {
    // 获取临时路径 chrome有效，其他浏览器的方法请自行查找
    const url = window.webkitURL.createObjectURL(file)
    const qr = new QrCode()
    // 解析二维码，返回promise
    return qr.decodeFromImage(url)
}
const { proxy } = getCurrentInstance() as any;


// const uploadRef = ref()
// const fileList = ref<any>([])
const qrCodeInfo = ref('')
  // import { computed } from 'vue';
  // import { useUserStore } from '/@/store/modules/user';
  // import { setLang } from '/@/i18n';
  // import { useI18n } from 'vue-i18n';
  // const { locale } = useI18n();

  // let cellList = ['vue3', 'vite', 'vue-router', 'axios', 'Pinia', 'vue-i18n', 'vue-jsx', 'vatlet/vant/nutUI'];
  // const userStore = useUserStore();
  // const getUserInfo = computed(() => {
  //   const { name = '' } = userStore.getUserInfo || {};
  //   return name;
  // });

  // const changeLang = (type) => {
  //   setLang(type);
  // };
  // const beforeUpload = async (file: File[]) => {
  //   return {
  //     uploadUrl:'',
  //     beforeUpload,
  //   };
  // }
  const onDecode = (data) => {
      // state.data = data
      qrCodeInfo.value = data
    }
  const beforeXhrUpload=(xhr:XMLHttpRequest,options:any)=>{
    options.onSuccess()
    // console.log(uploaderRef.value.fileList);
    
    // if (options.method.toLowerCase() == 'put') {
    //   xhr.send(options.sourceFile);
    // }else{
    //   xhr.send(options.formData);
    // }
  }
  const copy = () => {
    try {
          toClipboard(qrCodeInfo.value);
          proxy.$toast.text('复制成功！');
      } catch (e) {
          proxy.$toast.text('复制失败！');
      }
  };
  const afterRead = (file) => {
      // 此时可以自行将文件上传至服务器
      const result = getQrUrl(file.file)// 二维码解码
      result.then(res => {
        if (res.data) {
            qrCodeInfo.value = res.data
        }
      }).catch(err => {
          console.log(err);
      })
  };
</script>
<style lang="scss">
  .header {
    display: flex;
    justify-content: center;
    align-items: center;
    padding: 0 20px;
    font-size: 40px;
    img {
      width: 90px;
      height: 90px;
    }
  }

  .intro-header {
    margin-top: 20px;
    display: flex;
    align-items: center;
    justify-content: center;
    font-size: 16px;
  }

  .supportList {
    margin: 0 16px;

    .nut-cell-group__title {
      margin-top: 30px;
    }
    .nut-icon {
      color: green;
    }
  }

  .btn-wrap {
    margin: 20px;
  }
  .btn-confirm {
    @include main-lang-bg(302px, 82px, '/@/assets/button', 'confirm.png');
  }
</style>
