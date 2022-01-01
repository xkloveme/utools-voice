<template>
  <div class="voice-home">

    <main>

      <div>
        <textarea rows="10" class="
          shadow-sm
          focus-visible:ring-blue-500
          focus:border-blue-500
          focus-visible:border-blue-500
          focus:outline-none
          focus:ring-2 focus:ring-blue-600
          focus:border-transparent
          block
          w-full
          sm:text-sm
          border border-gray-300
          rounded-md
          font-bold
          leading-tight
          text-gray-900
        " v-model="value" placeholder="你好欢迎使用自然语音,您可以在输入想要朗读的内容"></textarea>
      </div>
    </main>
    <nav class="bg-gray-800">
      <div class="max-w-screen-xl mx-auto px-4 sm:px-4 lg:px-6">
        <div class="flex items-center justify-between h-12">
          <div class="flex items-center">
            <div class="relative text-white">
              <select @change="changePeople" class="focus:ring-indigo-500 focus:border-gray-500 h-full py-0 pl-0 pr-7 bg-white border-indigo-600 text-blue-900 sm:text-sm rounded-md">
                <option v-for="(item,i) in list" :key="i" :value="item.url">{{item.name}}</option>
              </select>
            </div>
            <div class="md:block">
              <div class="ml-10 flex items-baseline">
                <a @click="startPlayOrPause()" class="
                    px-5
                    py-2
                    rounded-md
                    text-sm
                    font-medium
                    cursor-pointer hover:text-gray-300 text-white hover:bg-gray-700 focus:outline-none focus:text-white bg-blue-700">{{ audio.playing?'暂停':'播放' }}</a>
                <a @click="changeSpeed()" class="
                    px-5
                    py-2
                    rounded-md
                    text-sm
                    font-medium
                    cursor-pointer hover:text-gray-300 text-white hover:bg-gray-700 focus:outline-none focus:text-white bg-indigo-700 ml-4">速度X{{ audio.speed }}</a>
                <!-- <a @click="changedrawType()" class="
                    px-5
                    py-2
                    rounded-md
                    text-sm
                    font-medium
                    cursor-pointer hover:text-gray-300 text-white hover:bg-gray-700 focus:outline-none focus:text-white bg-green-700 ml-4">场景{{drawType}}</a> -->
                <a @click="downloadMp3" class="
                    px-5
                    py-2
                    rounded-md
                    text-sm
                    font-medium
                    cursor-pointer hover:text-gray-300 text-white hover:bg-gray-700 focus:outline-none focus:text-white bg-red-700 ml-4">下载</a>
              </div>
            </div>
          </div>
          <div class="md:block">
            <div class="ml-4 flex items-center md:ml-6">
              <!-- Profile dropdown -->
              <div class="ml-3 relative">
                <div>
                  <button class="
                    max-w-xs
                    flex
                    items-center
                    text-sm
                    rounded-full
                    text-white
                    focus:outline-none
                    focus:shadow-solid
                  " id="user-menu" aria-label="User menu" aria-haspopup="true" @click="open('https://github.com/xkloveme')">
                    <img class="h-8 w-8 rounded-full" src="@/assets/avatar.jpg" alt="" />
                  </button>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </nav>
    <audio ref="audio" @pause="onPause" @timeupdate="onTimeupdate" @loadedmetadata="onLoadedmetadata" @play="onPlay">
      <source :src="url" type="audio/mp3">
      <source :src="url" type="audio/ogg">
    </audio>
    <div class="mt-40 absolute w-full" v-show="drawType=='1'">
      <div class="range-container">
        <input v-model="sliderTime" type="range" id="range" @change="changeCurrentTime" min="0" max="100">
        <label for="range" :style="{left:sliderTime+'%'}">{{sliderTime}}%</label>
      </div>
    </div>
    <div v-show="drawType=='3'" ref="line" class="absolute w-full" style="min-height:50%">
      33
    </div>

    <div class="stars animate-ping duration-10000"></div>
    <div id="stars2" class="animate-pulse"></div>
    <div id="stars3" class="animate-pulse"></div>
    <div class="stars animate-pulse"></div>
    <div class="stars animate-pulse"></div>
  </div>
</template>

<script>
import { defineComponent } from 'vue'
// import { MusicVisualizer } from '@/assets/js/MusicVisualizer.js'
export default defineComponent({
  data: () => ({
    timer: null,
    value: '',
    mv: null, // 场景
    drawTypeList: ['1', '2', '3'],
    drawType: '1',//场景一
    isExactActive: 0,
    selectUrl: 'https://s4w-2019.zhiyin.sogou.com/tts?language_code=zh-cmn-Hans-CN&speaker=kangge-pro&text={{encodeURIComponent(speakText)}}&speaking_rate=1&volume=1',
    speeds: [1, 1.5, 2, 3, 4],
    sliderTime: 0, //进度条
    audio: {
      currentTime: 0,
      maxTime: 0,
      playing: false,// 该字段是音频是否处于播放状态的属性
      muted: false,
      speed: 1,
      waiting: true,
      preload: 'auto'
    },
    url: "https://s4w-2019.zhiyin.sogou.com/tts?language_code=zh-cmn-Hans-CN&speaker=kangge-pro&text=您好!欢迎使用自然语音阅读软件,您可以在上方输入想要朗读的内容,祝你玩的愉快,如果您觉得不错,请帮助开发者喝杯咖啡 ☕️,一分也是情,一分也是爱 ❤️ 当然您的鼓励会让我走的更远.&speaking_rate=1&volume=1",
    list: [
      {
        "name": "B AI搜狗-康哥",
        "url": "https://s4w-2019.zhiyin.sogou.com/tts?language_code=zh-cmn-Hans-CN&speaker=kangge-pro&text={{encodeURIComponent(speakText)}}&speaking_rate=1&volume=1"
      },
      {
        "name": "B AI搜狗-阿华 低沉 缓",
        "url": "https://s4w-2019.zhiyin.sogou.com/tts?language_code=zh-cmn-Hans-CN&speaker=ahua-pro&text={{encodeURIComponent(speakText)}}&speaking_rate=1&volume=1"
      },
      {
        "name": "B AI搜狗-阿星",
        "url": "https://s4w-2019.zhiyin.sogou.com/tts?language_code=zh-cmn-Hans-CN&speaker=axing-pro&text={{encodeURIComponent(speakText)}}&speaking_rate=1&volume=1"
      },
      {
        "name": "B AI搜狗-青峰 磁性 缓",
        "url": "https://s4w-2019.zhiyin.sogou.com/tts?language_code=zh-cmn-Hans-CN&speaker=qingfeng-pro&text={{encodeURIComponent(speakText)}}&speaking_rate=1&volume=1"
      },
      {
        "name": "B0002 度小宇",
        "url": "http://tts.baidu.com/text2audio,{\n    \"method\": \"POST\",\n    \"body\": \"tex={{encodeURI(encodeURI(speakText))}}&spd={{(speakSpeed + 5) / 10 + 4}}&per=2&cuid=baidu_speech_demo&idx=1&cod=2&lan=zh&ctp=1&pdt=301&vol=5&aue=6&pit=5&_res_tag_=audio\"\n}"
      },
      {
        "name": "B0106 度博文",
        "url": "http://tts.baidu.com/text2audio,{\n    \"method\": \"POST\",\n    \"body\": \"tex={{encodeURI(encodeURI(speakText))}}&spd={{(speakSpeed + 5) / 10 + 4}}&per=106&cuid=baidu_speech_demo&idx=1&cod=2&lan=zh&ctp=1&pdt=301&vol=5&aue=6&pit=5&_res_tag_=audio\"\n}"
      },
      {
        "name": "B4003 度逍遥",
        "url": "http://tsn.baidu.com/text2audio,{\n    \"method\": \"POST\",\n    \"body\": \"tex={{encodeURI(encodeURI(speakText))}}&spd={{(speakSpeed + 5) / 10 + 4}}&per=4003&cuid=baidu_speech_demo&idx=1&cod=2&lan=zh&ctp=1&pdt=232&vol=5&aue=6&pit=5&_res_tag_=audio\"\n}"
      },
      {
        "name": "B4003 度逍遥②",
        "url": "http://tts.baidu.com/text2audio,{\n    \"method\": \"POST\",\n    \"body\": \"tex={{encodeURI(encodeURI(speakText))}}&spd={{(speakSpeed + 5) / 10 + 4}}&per=3&cuid=baidu_speech_demo&idx=1&cod=2&lan=zh&ctp=1&pdt=160&vol=5&aue=6&pit=5&_res_tag_=audio\"\n}"
      },
      {
        "name": "B4106 温和儒雅",
        "url": "http://tts.baidu.com/text2audio,{\n    \"method\": \"POST\",\n    \"body\": \"tex={{encodeURI(encodeURI(speakText))}}&spd={{(speakSpeed + 5) / 10 + 4}}&per=4106&cuid=baidu_speech_demo&idx=1&cod=2&lan=zh&ctp=1&pdt=301&vol=5&aue=6&pit=5&_res_tag_=audio\"\n}"
      },
      {
        "name": "B4114 百度评书AI",
        "url": "http://ai.baidu.com/aidemo?type=tns&spd=5&rate=32&pit=5&vol=15&dt=1&per=4114&tex={{encodeURI(encodeURI(speakText))}}"
      },
      {
        "name": "B4115 情感男声",
        "url": "http://tsn.baidu.com/text2audio,{\n    \"method\": \"POST\",\n    \"body\": \"tex={{encodeURI(encodeURI(speakText))}}&spd={{(speakSpeed + 5) / 10 + 4}}&per=4115&cuid=baidu_speech_demo&idx=1&cod=2&lan=zh&ctp=1&pdt=211&vol=5&aue=6&pit=5&_res_tag_=audio\"\n}"
      },
      {
        "name": "B4121 青年男声",
        "url": "http://tsn.baidu.com/text2audio,{\n    \"method\": \"POST\",\n    \"body\": \"tex={{encodeURI(encodeURI(speakText))}}&spd={{(speakSpeed + 5) / 10 + 4}}&per=4121&cuid=baidu_speech_demo&idx=1&cod=2&lan=zh&ctp=1&pdt=232&vol=5&aue=6&pit=5&_res_tag_=audio\"\n}"
      },
      {
        "name": "B4123 百度解说",
        "url": "http://tts.baidu.com/text2audio,{\n    \"method\": \"POST\",\n    \"body\": \"tex={{encodeURI(encodeURI(speakText))}}&spd={{(speakSpeed + 5) / 10 + 4}}&per=4123&cuid=baidu_speech_demo&idx=1&cod=2&lan=zh&ctp=1&pdt=12&vol=5&aue=6&pit=5&_res_tag_=audio\"\n}"
      },
      {
        "name": "B4127 激情澎湃",
        "url": "http://tts.baidu.com/text2audio,{\n\"method\": \"POST\",\n\"body\": \"tex={{encodeURI(encodeURI(speakText))}}&spd={{(speakSpeed + 4) / 10 + 3}}&per=4127&cuid=baidu_speech_demo&idx=1&cod=2&lan=zh&ctp=1&pdt=11&vol=6&aue=6&pit=5&_res_tag_=audio\"\n}"
      },
      {
        "name": "B4127 激情澎湃②",
        "url": "http://tts.baidu.com/text2audio,{\n    \"method\": \"POST\",\n    \"body\": \"tex={{encodeURI(encodeURI(speakText))}}&spd={{(speakSpeed + 5) / 10 + 4}}&per=4127&cuid=baidu_speech_demo&idx=1&cod=2&lan=zh&ctp=1&pdt=11&vol=5&aue=6&pit=3&_res_tag_=audio\"\n}"
      },
      {
        "name": "B4127 百度主持",
        "url": "http://tts.baidu.com/text2audio,{\n    \"method\": \"POST\",\n    \"body\": \"tex={{encodeURI(encodeURI(speakText))}}&spd={{(speakSpeed + 5) / 10 + 4}}&per=4127&cuid=baidu_speech_demo&idx=1&cod=2&lan=zh&ctp=1&pdt=11&vol=5&aue=6&pit=5&_res_tag_=audio\"\n}"
      },
      {
        "name": "B4127 磁性男声",
        "url": "http://tts.baidu.com/text2audio,{\n    \"method\": \"POST\",\n    \"body\": \"tex={{encodeURI(encodeURI(speakText))}}&spd={{(speakSpeed + 5) / 10 + 4}}&per=4127&cuid=baidu_speech_demo&idx=1&cod=2&lan=zh&ctp=1&pdt=11&vol=5&aue=6&pit=3&_res_tag_=audio\"\n}"
      },
      {
        "name": "B4128 醇厚男声",
        "url": "http://tts.baidu.com/text2audio,{\n    \"method\": \"POST\",\n    \"body\": \"tex={{encodeURI(encodeURI(speakText))}}&spd={{(speakSpeed + 5) / 10 + 4}}&per=4128&cuid=baidu_speech_demo&idx=1&cod=2&lan=zh&ctp=1&pdt=12&vol=5&aue=6&pit=5&_res_tag_=audio\"\n}"
      },
      {
        "name": "B4129 青少年音",
        "url": "http://tts.baidu.com/text2audio,{\n    \"method\": \"POST\",\n    \"body\": \"tex={{encodeURI(encodeURI(speakText))}}&spd={{(speakSpeed + 5) / 10 + 4}}&per=4129&cuid=baidu_speech_demo&idx=1&cod=2&lan=zh&ctp=1&pdt=12&vol=5&aue=6&pit=5&_res_tag_=audio\"\n}"
      },
      {
        "name": "B4218 百度解说",
        "url": "http://tts.baidu.com/text2audio,{\n    \"method\": \"POST\",\n    \"body\": \"tex={{encodeURI(encodeURI(speakText))}}&spd={{(speakSpeed + 5) / 10 + 4}}&per=4128&cuid=baidu_speech_demo&idx=1&cod=2&lan=zh&ctp=1&pdt=12=&vol=5&aue=6&pit=5&_res_tag_=audio\"\n}"
      },
      {
        "name": "B5003 磁性男声①",
        "url": "http://tsn.baidu.com/text2audio,{\n    \"method\": \"POST\",\n    \"body\": \"tex={{encodeURI(encodeURI(speakText))}}&spd={{(speakSpeed + 5) / 10 + 4}}&per=5003&cuid=baidu_speech_demo&idx=1&cod=2&lan=zh&ctp=1&pdt=232&vol=5&aue=6&pit=5&_res_tag_=audio\"\n}"
      },
      {
        "name": "B5003 磁性男声②",
        "url": "http://tsn.baidu.com/text2audio,{\n    \"method\": \"POST\",\n    \"body\": \"tex={{encodeURI(encodeURI(speakText))}}&spd={{String((speakSpeed + 5) / 10 + 4)}}&per=5003&cuid=baidu_speech_demo&idx=1&cod=2&lan=zh&ctp=1&pdt=220&vol=5&pit=5&_res_tag_=audio\"\n}"
      },
      {
        "name": "G AI搜狗-夕月 御姐",
        "url": "https://s4w-2019.zhiyin.sogou.com/tts?language_code=zh-cmn-Hans-CN&speaker=xiyue-pro&text={{encodeURIComponent(speakText)}}&speaking_rate=1&volume=1"
      },
      {
        "name": "G AI搜狗-婉清 冷清",
        "url": "https://s4w-2019.zhiyin.sogou.com/tts?language_code=zh-cmn-Hans-CN&speaker=wanqing-pro&text={{encodeURIComponent(speakText)}}&speaking_rate=1&volume=1"
      },
      {
        "name": "G AI搜狗-若曦 柔和缓慢",
        "url": "https://s4w-2019.zhiyin.sogou.com/tts?language_code=zh-cmn-Hans-CN&speaker=xf5-pro&text={{encodeURIComponent(speakText)}}&speaking_rate=1&volume=1"
      },
      {
        "name": "G AI搜狗-雅妮 温柔",
        "url": "https://s4w-2019.zhiyin.sogou.com/tts?language_code=zh-cmn-Hans-CN&speaker=yani-pro&text={{(encodeURI(speakText))}}&speaking_rate=1&volume=1"
      },
      {
        "name": "G 小爱同学-蜜糖",
        "url": "https://xbdhgj.ksmobile.com/api/voice/repost_tts?pdt=7247&&token=03AGdBq24_MmdU0dH25IaHZee43wf4PqmCBleNwjqGZzHwo_mlCzJqVNSVpH-mZhy8Bq5zjhXCQdNZ-dc9F3ni5WID0WfvkKmYmDqQIvdsNa3wv_O1JQ-t0qs5hpZn1EYb6yBmAtGqQGAQuggQydonEMWf3-QmqH0ANlTSqlUwCxYuYry2_Jb30Y5vxDwGaaX_C4fZkm7RDBAI4h6hg9IhT22xb8LxM7H5xxbEht-8cKry7KSgy61EcxbGRntufqujF98kQc0lh0uSUH7miN1TqEUEKeXf1Z7pS_mBUqtDtZmGo6OHHD3Hh0CchRKgIqgBWUJxD5z8dkSPn9lq7HOhrMuDHM7PgO6WaZLOlWlbeLNngp3pQevjKhz-hgV7HCRNGbpVTDNttH9393BLC9NivSQdAFkepNROxdGAX3an5wF7ZdO3zGxdZWnofS61BjMjjn_Q6sPw9aa4&sn=77482f17-d83c-12a6-9dee-5681b89686d4&sid=77482f17-d83c-12a6-9dee-5681b89686d4&tex={{encodeURI(encodeURI(speakText))}}&per=0&pit=5&vol=5&spd=5\n\n"
      },
      {
        "name": "G 微软小娜",
        "url": "https://o-oo.net.cn/speak.php?a={{encodeURIComponent(speakText)}}&b=zh-chs"
      },
      {
        "name": "G 思必驰 灵动女声",
        "url": "https://dds.dui.ai/runtime/v1/synthesize?voiceId=madoufp_yubo&text={{encodeURI(encodeURI(speakText))}}&speed=1&volume=100&audioType=wav"
      },
      {
        "name": "G 讯飞小燕 （稍僵硬）",
        "url": "http://120.24.87.124/cgi-bin/ekho2.pl?cmd=SPEAK&voice=iflytek&speedDelta=0&pitchDelta=0&volumeDelta=0&text={{encodeURI(encodeURI(speakText))}} "
      },
      {
        "name": "G 讯飞小琳（台普）",
        "url": "http://120.24.87.124/cgi-bin/ekho2.pl?cmd=SPEAK&voice=iflytekXiaolin&speedDelta=0&pitchDelta=0&volumeDelta=0&text={{encodeURI(encodeURI(speakText))}}\n"
      },
      {
        "name": "G 讯飞小美（粤语）",
        "url": "http://120.24.87.124/cgi-bin/ekho2.pl?cmd=SPEAK&voice=iflytekXiaomei&speedDelta=0&pitchDelta=0&volumeDelta=0&text={{encodeURI(encodeURI(speakText))}}\n"
      },
      {
        "name": "G 谷歌中文",
        "url": "https://www.google.cn/speech-api/v2/synthesize?key=AIzaSyCkfPOPZXDKNn8hhgu3JrA62wIgC93d44k&enc=mpeg&client=chromium&text={{encodeURIComponent(speakText)}}&lang=zh-ch&speed=0.5"
      },
      {
        "name": "G 谷歌台湾",
        "url": "https://www.google.cn/speech-api/v2/synthesize?key=AIzaSyCkfPOPZXDKNn8hhgu3JrA62wIgC93d44k&enc=mpeg&client=chromium&text={{encodeURIComponent(speakText)}}&lang=zh-tw&speed=0.5\n\n"
      },
      {
        "name": "G 谷歌粤语",
        "url": "https://www.google.cn/speech-api/v2/synthesize?key=AIzaSyCkfPOPZXDKNn8hhgu3JrA62wIgC93d44k&enc=mpeg&client=chromium&text={{encodeURIComponent(speakText)}}&lang=zh-HK&speed=0.5\n\n"
      },
      {
        "name": "G 鬼畜女声（普通话）",
        "url": "http://120.24.87.124/cgi-bin/ekho2.pl?cmd=SPEAK&voice=Mandarin&speedDelta=0&pitchDelta=0&volumeDelta=0&text={{encodeURIComponent(speakText)}}\n\n"
      },
      {
        "name": "G0000 度小美",
        "url": "http://tts.baidu.com/text2audio,{\n    \"method\": \"POST\",\n    \"body\": \"tex={{encodeURI(encodeURI(speakText))}}&spd={{(speakSpeed + 5) / 10 + 4}}&per=0&cuid=baidu_speech_demo&idx=1&cod=2&lan=zh&ctp=1&pdt=160&vol=5&aue=6&pit=5&_res_tag_=audio\"\n}"
      },
      {
        "name": "G0004 度丫丫",
        "url": "http://tts.baidu.com/text2audio,{\n    \"method\": \"POST\",\n    \"body\": \"tex={{encodeURI(encodeURI(speakText))}}&spd={{(speakSpeed + 5) / 10 + 4}}&per=4&cuid=baidu_speech_demo&idx=1&cod=2&lan=zh&ctp=1&pdt=301&vol=5&aue=6&pit=5&_res_tag_=audio\"\n}"
      },
      {
        "name": "G0005 度小娇",
        "url": "http://tsn.baidu.com/text2audio,{\n    \"method\": \"POST\",\n    \"body\": \"tex={{encodeURI(encodeURI(speakText))}}&spd={{(speakSpeed + 4) / 10 + 3}}&per=5&cuid=baidu_speech_demo&idx=1&cod=2&lan=zh&ctp=1&pdt=211&vol=5&aue=6&pit=5&_res_tag_=audio\"\n}"
      },
      {
        "name": "G0100 标准女声",
        "url": "http://tts.baidu.com/text2audio,{\n    \"method\": \"POST\",\n    \"body\": \"tex={{encodeURI(encodeURI(speakText))}}&spd={{(speakSpeed + 5) / 10 + 3}}&per=100&cuid=baidu_speech_demo&idx=1&cod=2&lan=zh&ctp=1&pdt=160&vol=5&aue=6&pit=5&_res_tag_=audio\"\n}"
      },
      {
        "name": "G0103 可可爱爱",
        "url": "http://tts.baidu.com/text2audio,{\n    \"method\": \"POST\",\n    \"body\": \"tex={{encodeURI(encodeURI(speakText))}}&spd={{(speakSpeed + 5) / 10 + 4}}&per=103&cuid=baidu_speech_demo&idx=1&cod=2&lan=zh&ctp=1&pdt=301&vol=5&aue=6&pit=5&_res_tag_=audio\"\n}"
      },
      {
        "name": "G0110 度小童",
        "url": "http://tsn.baidu.com/text2audio,{\n    \"method\": \"POST\",\n    \"body\": \"tex={{encodeURI(encodeURI(speakText))}}&spd={{(speakSpeed + 5) / 10 + 4}}&per=110&cuid=baidu_speech_demo&idx=1&cod=2&lan=zh&ctp=1&pdt=232&vol=5&aue=6&pit=5&_res_tag_=audio\"\n}"
      },
      {
        "name": "G0111 度小萌",
        "url": "http://tsn.baidu.com/text2audio,{\n    \"method\": \"POST\",\n    \"body\": \"tex={{encodeURI(encodeURI(speakText))}}&spd={{(speakSpeed + 5) / 10 + 4}}&per=111&cuid=baidu_speech_demo&idx=1&cod=2&lan=zh&ctp=1&pdt=220&vol=5&aue=6&pit=5&_res_tag_=audio\"\n}"
      },
      {
        "name": "G1100 度小乔",
        "url": "http://tts.baidu.com/text2audio,{\n    \"method\": \"POST\",\n    \"body\": \"tex={{encodeURI(encodeURI(speakText))}}&spd={{(speakSpeed + 5) / 10 + 4}}&per=1100&cuid=baidu_speech_demo&idx=1&cod=2&lan=zh&ctp=1&pdt=160=&vol=5&aue=6&pit=3&_res_tag_=audio\"\n}"
      },
      {
        "name": "G1200 普通女声",
        "url": "http://tts.baidu.com/text2audio,{\n    \"method\": \"POST\",\n    \"body\": \"tex={{encodeURI(encodeURI(speakText))}}&spd={{(speakSpeed + 5) / 10 + 4}}&per=1200&cuid=baidu_speech_demo&idx=1&cod=2&lan=zh&ctp=1&pdt=160&vol=&rate=32=5&pit=5&_res_tag_=audio\"\n}"
      },
      {
        "name": "G2200 低哑女声",
        "url": "http://tts.baidu.com/text2audio,{\n    \"method\": \"POST\",\n    \"body\": \"tex={{encodeURI(encodeURI(speakText))}}&spd={{(speakSpeed + 5) / 10 + 4}}&per=2200&cuid=baidu_speech_demo&idx=1&cod=2&lan=zh&ctp=1&pdt=11&vol=6&aue=6&pit=5&_res_tag_=audio\"\n}"
      },
      {
        "name": "G4007 台湾女声",
        "url": "http://tts.baidu.com/text2audio,{\n    \"method\": \"POST\",\n    \"body\": \"tex={{encodeURI(encodeURI(speakText))}}&spd={{(speakSpeed + 5) / 10 + 4}}&per=4007&cuid=baidu_speech_demo&idx=1&cod=2&lan=zh&ctp=1&pdt=232&vol=5&aue=6&pit=5&_res_tag_=audio\"\n}"
      },
      {
        "name": "G4007台湾女声 ",
        "url": "http://tts.baidu.com/text2audio,{\n    \"method\": \"POST\",\n    \"body\": \"tex={{encodeURI(encodeURI(speakText))}}&spd={{(speakSpeed + 5) / 10 + 4}}&per=4007&cuid=baidu_speech_demo&idx=1&cod=2&lan=zh&ctp=1&pdt=220&vol=5&aue=6&pit=5&_res_tag_=audio\"\n}"
      },
      {
        "name": "G4100 温暖女声",
        "url": "http://tsn.baidu.com/text2audio,{\n    \"method\": \"POST\",\n    \"body\": \"tex={{encodeURI(encodeURI(speakText))}}&spd={{(speakSpeed + 5) / 10 + 4}}&per=4100&cuid=baidu_speech_demo&idx=1&cod=2&lan=zh&ctp=1&pdt=211=&vol=5&aue=6&pit=5&_res_tag_=audio\"\n}"
      },
      {
        "name": "G4103 卡哇伊",
        "url": "http://tsn.baidu.com/text2audio,{\n    \"method\": \"POST\",\n    \"body\": \"tex={{encodeURI(encodeURI(speakText))}}&spd={{(speakSpeed + 5) / 10 + 4}}&per=4103&cuid=baidu_speech_demo&idx=1&cod=2&lan=zh&ctp=1&pdt=232&vol=5&aue=6&pit=5&_res_tag_=audio\"\n}"
      },
      {
        "name": "G4105 情感女声",
        "url": "http://tts.baidu.com/text2audio,{\n\"method\": \"POST\",\n\"body\": \"tex={{encodeURI(encodeURI(speakText))}}&spd={{(speakSpeed + 4) / 10 + 3}}&per=4105&cuid=baidu_speech_demo&idx=1&cod=2&lan=zh&ctp=1&pdt=220&vol=6&aue=6&pit=5&_res_tag_=audio\"\n}"
      },
      {
        "name": "G4117 优雅女声",
        "url": "http://tsn.baidu.com/text2audio,{\n    \"method\": \"POST\",\n    \"body\": \"tex={{encodeURI(encodeURI(speakText))}}&spd={{(speakSpeed + 5) / 10 + 4}}&per=4117&cuid=baidu_speech_demo&idx=1&cod=2&lan=zh&ctp=1&pdt=211&vol=5&aue=6&pit=5&_res_tag_=audio\"\n}"
      },
      {
        "name": "G4118 温柔女声",
        "url": "http://tts.baidu.com/text2audio,{\n    \"method\": \"POST\",\n    \"body\": \"tex={{encodeURI(encodeURI(speakText))}}&spd={{(speakSpeed + 5) / 10 + 4}}&per=4118&cuid=baidu_speech_demo&idx=1&cod=2&lan=zh&ctp=1&pdt=232&vol=5&aue=6&pit=5&_res_tag_=audio\"\n}"
      },
      {
        "name": "G4119 度小鹿",
        "url": "http://tsn.baidu.com/text2audio,{\n    \"method\": \"POST\",\n    \"body\": \"tex={{encodeURI(encodeURI(speakText))}}&spd={{(speakSpeed + 5) / 10 + 4}}&per=4119&cuid=baidu_speech_demo&idx=1&cod=2&lan=zh&ctp=1&pdt=220&vol=5&aue=6&pit=5&_res_tag_=audio\"\n}"
      },
      {
        "name": "G4125 沙雕女声",
        "url": "http://tts.baidu.com/text2audio,{\n\"method\": \"POST\",\n\"body\": \"tex={{encodeURI(encodeURI(speakText))}}&spd={{(speakSpeed + 4) / 10 + 3}}&per=4125&cuid=baidu_speech_demo&idx=1&cod=2&lan=zh&ctp=1&pdt=211&vol=6&aue=6&pit=5&_res_tag_=audio\"\n}"
      },
      {
        "name": "G4126 缓慢女声",
        "url": "http://tts.baidu.com/text2audio,{\n    \"method\": \"POST\",\n    \"body\": \"tex={{encodeURI(encodeURI(speakText))}}&spd={{(speakSpeed + 5) / 10 + 4}}&per=4126&cuid=baidu_speech_demo&idx=1&cod=2&lan=zh&ctp=1&pdt=211&vol=5&aue=6&pit=5&_res_tag_=audio\"\n}"
      },
      // {
      //   "name": "G5100 轻快女声",
      //   "url": "http://tts.baidu.com/text2audio,{\n    \"method\": \"POST\",\n    \"body\": \"tex={{encodeURI(encodeURI(speakText))}}&spd={{(speakSpeed + 5) / 10 + 4}}&per=5100&cuid=baidu_speech_demo&idx=1&cod=2&lan=zh&ctp=1&pdt=160=&vol=5&aue=6&pit=5&_res_tag_=audio\"\n}"
      // },
      // {
      //   "name": "G5105度灵儿 ",
      //   "url": "http://tts.baidu.com/text2audio,{\n    \"method\": \"POST\",\n    \"body\": \"tex={{encodeURI(encodeURI(speakText))}}&spd={{(speakSpeed + 5) / 10 + 4}}&per=5105&cuid=baidu_speech_demo&idx=1&cod=2&lan=zh&ctp=1&pdt=160=&vol=5&aue=6&pit=5&_res_tag_=audio\"\n}"
      // },
      // {
      //   "name": "G5117 甜美女声",
      //   "url": "http://tts.baidu.com/text2audio,{\n    \"method\": \"POST\",\n    \"body\": \"tex={{encodeURI(encodeURI(speakText))}}&spd={{(speakSpeed + 5) / 10 + 4}}&per=5117&cuid=baidu_speech_demo&idx=1&cod=2&lan=zh&ctp=1&pdt=220&vol=5&aue=6&pit=5&_res_tag_=audio\"\n}"
      // },
      {
        "name": "G5118 优美女声",
        "url": "http://tsn.baidu.com/text2audio,{\n    \"method\": \"POST\",\n    \"body\": \"tex={{encodeURI(encodeURI(speakText))}}&spd={{(speakSpeed + 5) / 10 + 4}}&per=5118&cuid=baidu_speech_demo&idx=1&cod=2&lan=zh&ctp=1&pdt=211=&vol=5&aue=6&pit=5&_res_tag_=audio\"\n}"
      },
      {
        "name": "小说听书自用版",
        "url": "http://tsn.baidu.com/text2audio,{\n\"method\": \"POST\",\n\"body\": \"tex={{encodeURI(encodeURI(speakText))}}&spd={{(speakSpeed + 4) / 10 + 3}}&per=4114&cuid=baidu_speech_demo&idx=1&cod=2&lan=zh&ctp=1&pdt=220&vol=6&aue=6&pit=5&_res_tag_=audio\"\n}"
      },
      {
        "name": "百度解说③4129",
        "url": "http://tts.baidu.com/text2audio,{\n    \"method\": \"POST\",\n    \"body\": \"tex={{encodeURI(encodeURI(speakText))}}&spd={{(speakSpeed + 5) / 10 + 4}}&per=4129&cuid=baidu_speech_demo&idx=1&cod=2&lan=zh&ctp=1&pdt=12&vol=5&aue=6&pit=5&_res_tag_=audio\"\n}"
      },
      {
        "name": "磁性男声 精修版5003",
        "url": "http://tsn.baidu.com/text2audio,{\n    \"method\": \"POST\",\n    \"body\": \"tex={{encodeURI(encodeURI(speakText))}}&spd={{(speakSpeed + 5) / 10 + 4}}&per=5003&cuid=baidu_speech_demo&idx=1&cod=2&lan=zh&ctp=1&pdt=220=&vol=5&aue=6&pit=5&_res_tag_=audio\"\n}"
      },
      {
        "name": "讯飞小燕",
        "url": "http://120.24.87.124/cgi-bin/ekho2.pl?cmd=SPEAK&voice=iflytek&speedDelta=0&pitchDelta=0&volumeDelta=0&text={{encodeURI(encodeURI(speakText))}}\n"
      }
    ]
  }),
  mounted () {
    // console.log(MusicVisualizer,88)
    this.init()

    // let self = this
    // document.onkeydown = function (e) {
    //   var keyCode = e.keyCode || e.which || e.charCode
    //   var ctrlKey = e.ctrlKey || e.metaKey
    //   if (ctrlKey && keyCode == 49) {
    //     self.copy()
    //     return false
    //   }
    //   if (ctrlKey && keyCode == 83) {
    //     self.getTrans(this.isExactActive)
    //     return false
    //   }
    // }
    // utools &&
    //   utools.onPluginEnter(({ code, type, payload, optional }) => {
    //     console.log('用户进入插件', code, type, payload)
    //     utools &&
    //       utools.setSubInput(({ text }) => {
    //         if (text) {
    //           this.value = text
    //         }
    //       }, '翻译')
    //     if (payload) {
    //       this.value = payload
    //     }
    //     if (this.value) {
    //       setTimeout(() => {
    //         this.getTrans(this.isExactActive)
    //       }, 500)
    //     }
    //   })
  },
  methods: {
    init () {
      console.log(this.$refs.line.clientHeight, 8888)
      // this.mv = new MusicVisualizer({
      //   size: 128,
      //   visualizer: this.draw()
      // })
    },
    changePeople (e) {
      this.selectUrl = e.target.value
      this.chageUrl()
    },
    chageUrl () {
      let result = { 'speakText': this.value || '请输入您要朗读的内容', 'speakSpeed': 6 }
      let replaceDoubleBraces = (str) => {
        return str.replace(/{{(.+?)}}/g, (_, g1) => {
          if (g1.indexOf('speakText') > -1) {
            return result['speakText']
          }
          if (g1.indexOf('speakSpeed') > -1) {
            return result['speakSpeed']
          }
        })
      }
      if (this.selectUrl.indexOf(',') > -1) {
        let first = this.selectUrl.slice(0, this.selectUrl.indexOf(','));
        let last = this.selectUrl.slice(this.selectUrl.indexOf(',') + 1)
        let data = JSON.parse(last)
        this.url = first + "?" + replaceDoubleBraces(data.body)
        this.audio.playing = false
        this.sliderTime = 0
        this.audio.speed = 1
      } else {
        this.url = replaceDoubleBraces(this.selectUrl)
        this.audio.playing = false
        this.sliderTime = 0
        this.audio.speed = 1
      }
      this.$refs.audio.src = this.url
    },
    // 语音控制
    // 控制音频的播放与暂停
    startPlayOrPause () {
      if (this.audio.playing) {
        return this.pause()
      } else {
        if (this.value) {
          this.chageUrl()
          this.play()
        } else {
          this.play()
        }
      }
    },
    // 播放音频
    play () {
      console.log(this.mv)
      // this.mv.play(this.url,"#alltime","#time");
      this.$refs.audio.play()
    },
    // 暂停音频
    pause () {
      this.$refs.audio.pause()
    },
    // 当音频播放
    onPlay () {
      this.audio.playing = true
    },
    // 当音频暂停
    onPause () {
      this.audio.playing = false
    },
    changeSpeed () {
      let index = this.speeds.indexOf(this.audio.speed) + 1
      this.audio.speed = this.speeds[index % this.speeds.length]
      this.$refs.audio.playbackRate = this.audio.speed
    },
    // 进度条toolTip
    formatProcessToolTip (index = 0) {
      index = parseInt(this.audio.maxTime / 100 * index)
      return '进度条: ' + realFormatSecond(index)
    },
    // 播放跳转
    changeCurrentTime (e) {
      this.sliderTime = e.target.value
      this.$refs.audio.currentTime = parseInt(Number(e.target.value) / 100 * this.audio.maxTime)
    },
    // 当加载语音流元数据完成后，会触发该事件的回调函数
    // 语音元数据主要是语音的长度之类的数据
    onLoadedmetadata (res) {
      this.audio.waiting = false
      this.audio.maxTime = parseInt(res.target.duration)
    },
    // 当timeupdate事件大概每秒一次，用来更新音频流的当前播放时间
    onTimeupdate (res) {
      this.audio.currentTime = res.target.currentTime
      let max = parseInt(this.audio.currentTime / this.audio.maxTime * 100)
      this.sliderTime = max >= 100 ? 100 : max
    },
    downloadMp3 () {
      fetch(this.url).then(res => res.blob()).then(blob => {
        const a = document.createElement('a');
        document.body.appendChild(a)
        a.style.display = 'none'
        // 使用获取到的blob对象创建的url
        const url = window.URL.createObjectURL(blob);
        a.href = url;
        // 指定下载的文件名
        a.download = '语音音频.mp3';
        a.click();
        document.body.removeChild(a)
        // 移除blob对象的url
        window.URL.revokeObjectURL(url);
      });
    },
    // ----------------------------------------------------------------
    //切换场景
    changedrawType () {
      let index = this.drawTypeList.indexOf(this.drawType) + 1
      this.drawType = this.drawTypeList[index % this.drawTypeList.length]
    },
    draw (arr) {
      var height=this.$refs.line.clientHeight
      var width=this.$refs.line.clientWidth
      var line;
      var size = 128;
      var Dots = [];
      var canvas = document.createElement("canvas");
      var ctx = canvas.getContext("2d");
      ctx.clearRect(0, 0, width, height);
      var w = width / size;  //size == arr.length
      var cw = w * 0.7;
      var capH = cw > 10 ? 10 : cw;
      ctx.fillStyle = line;
      for (var i = 0; i < size; i++) {
        var o = Dots[i];
        if (this.drawType === "1") {
          var h = arr[i] / 256 * height;
          ctx.fillRect(w * i, height - h, cw, h);
          ctx.fillRect(w * i, height - (o.cap + capH), cw, capH);
          o.cap--;
          if (o.cap < 0) {
            o.cap = 0;
          }
          if (h > 0 && o.cap < h + 40) {
            o.cap = h + 40 > height - capH ? height - capH : h + 40;
          }
        } else if (this.drawType === "2") {
          ctx.beginPath();
          var r = 10 + arr[i] / 256 * (height > width ? width : height) / 10;
          ctx.arc(o.x, o.y, r, 0, Math.PI * 2, true);
          var g = ctx.createRadialGradient(o.x, o.y, 0, o.x, o.y, r);
          g.addColorStop(0, "#fff");
          g.addColorStop(1, o.color);
          ctx.fillStyle = g;
          ctx.fill();
          o.x += o.dx;
          o.x = o.x > width ? 0 : o.x;
        } else if (this.drawType === "3") {
          var baseH = height / 2 - 100;
          var baseW = width / 4;
          if (i == 0) {
            ctx.beginPath();
            ctx.lineWidth = 2;
            ctx.moveTo(0, baseH);
            ctx.lineTo(width, baseH);
            ctx.stroke();
            ctx.moveTo(width / 2, baseH);

            var grd = ctx.createLinearGradient(0, baseH, 0, height);
            grd.addColorStop(0, "rgba(10,112,216,.2)");
            grd.addColorStop(1, "rgba(8,8,8,.0)");

            ctx.fillStyle = grd;
            ctx.fillRect(0, baseH, width, height);
          } else {
            ctx.lineTo(width / 2 + i * cw, baseH - arr[i]);
            ctx.lineTo(width / 2 + i * cw, baseH + arr[i]);
            ctx.stroke();
            ctx.beginPath();

            ctx.lineTo(width / 2 - i * cw, baseH - arr[i]);
            ctx.lineTo(width / 2 - i * cw, baseH + arr[i]);
            ctx.stroke();
            ctx.beginPath();
            var lines = ctx.createLinearGradient(0, 0, width, height);
            // var color = "rgba("+random(0,255)+","+random(0,255)+","+random(0,255)+",1)"; 
            lines.addColorStop(0, "red");
            // if(i % 2 == 0){
            //      		lines.addColorStop(.5,color);
            // }else{
            //      		lines.addColorStop(0.5,"#5dad4d");
            // }
            lines.addColorStop(1, "blue");
            ctx.strokeStyle = lines;
            ctx.stroke();
          }
        }
      }
    },
    open (url) {
      window.open(url)
      window.openExternal(url)
    },
  },
})
</script>
<style scoped>
.voice-home {
  height: 100%;
  min-height: 100vh;
  background: radial-gradient(ellipse at bottom, #1b2735 0%, #090a0f 100%);
  overflow: hidden;
}
.range-container {
  position: relative;
  width: 80%;
  margin: 0 auto;
}

input[type='range'] {
  width: 100%;
  margin: 18px 0;
  -webkit-appearance: none;
}

input[type='range']:focus {
  outline: none;
}

input[type='range'] + label {
  position: absolute;
  top: -25px;
  left: 50%;
  width: 70px;
  padding: 5px 0;
  background-color: #fff;
  text-align: center;
  border-radius: 4px;
  box-shadow: 0 0 5px rgba(0, 0, 0, 0.3);
}

/* Chrome & Safari */
input[type='range']::-webkit-slider-runnable-track {
  background: #fff;
  width: 100%;
  height: 10px;
  cursor: pointer;
  box-shadow: 0 0 1px 1px rgb(0 0 0 / 25%), inset 0 1px rgb(255 255 255 / 10%);
  background-color: #1d7aa1;
  background-image: -webkit-linear-gradient(
    45deg,
    rgba(255, 255, 255, 0.2) 25%,
    transparent 25%,
    transparent 50%,
    rgba(255, 255, 255, 0.2) 50%,
    rgba(255, 255, 255, 0.2) 75%,
    transparent 75%,
    transparent
  );
}

input[type='range']::-webkit-slider-thumb {
  -webkit-appearance: none;
  height: 24px;
  width: 24px;
  background: rgb(223, 15, 136);
  border-radius: 50%;
  border: 1px solid purple;
  margin-top: -7px;
  cursor: pointer;
}

/* Firefox */
input[type='range']::-moz-range-track {
  background: rgb(223, 15, 136);
  border-radius: 4px;
  width: 100%;
  height: 13px;
  cursor: pointer;
}

input[type='range']::-moz-range-thumb {
  -webkit-appearance: none;
  height: 24px;
  width: 24px;
  background: rgb(223, 15, 136);
  border-radius: 50%;
  border: 1px solid purple;
  margin-top: -7px;
  cursor: pointer;
}

/* IE */
input[type='range']::-ms-track {
  background: rgb(223, 15, 136);
  border-radius: 4px;
  width: 100%;
  height: 13px;
  cursor: pointer;
}

input[type='range']::-ms-thumb {
  -webkit-appearance: none;
  height: 24px;
  width: 24px;
  background: rgb(223, 15, 136);
  border-radius: 50%;
  border: 1px solid purple;
  margin-top: -7px;
  cursor: pointer;
}

.stars {
  width: 1px;
  height: 1px;
  background: transparent;
  box-shadow: 1407px 511px #fff, 1611px 119px #fff, 1686px 956px #fff,
    1163px 1929px #fff, 912px 1242px #fff, 490px 469px #fff, 869px 425px #fff,
    1447px 891px #fff, 422px 1960px #fff, 517px 1995px #fff, 738px 171px #fff,
    1328px 1668px #fff, 874px 1490px #fff, 83px 81px #fff, 632px 98px #fff,
    1518px 1764px #fff, 636px 596px #fff, 1178px 131px #fff, 278px 1179px #fff,
    1898px 1951px #fff, 1787px 326px #fff, 186px 1588px #fff, 552px 1942px #fff,
    1929px 1300px #fff, 802px 681px #fff, 430px 1711px #fff, 1192px 308px #fff,
    123px 1604px #fff, 880px 169px #fff, 1400px 632px #fff, 500px 1165px #fff,
    288px 1208px #fff, 319px 1419px #fff, 1170px 980px #fff, 1608px 784px #fff,
    1735px 1276px #fff, 966px 1534px #fff, 654px 783px #fff, 1366px 964px #fff,
    1213px 60px #fff, 302px 1509px #fff, 845px 714px #fff, 524px 323px #fff,
    1538px 1399px #fff, 394px 619px #fff, 680px 26px #fff, 353px 776px #fff,
    1826px 1450px #fff, 1909px 1452px #fff, 1014px 1315px #fff,
    1883px 1474px #fff, 766px 1742px #fff, 1693px 658px #fff, 1186px 302px #fff,
    376px 1575px #fff, 712px 1739px #fff, 1627px 299px #fff, 482px 224px #fff,
    1379px 510px #fff, 1543px 1602px #fff, 45px 606px #fff, 827px 1336px #fff,
    224px 1939px #fff, 1098px 1342px #fff, 813px 1553px #fff, 825px 419px #fff,
    519px 894px #fff, 1406px 797px #fff, 1341px 274px #fff, 1787px 903px #fff,
    1701px 1483px #fff, 1108px 232px #fff, 1599px 1409px #fff, 659px 870px #fff,
    1538px 335px #fff, 632px 1855px #fff, 154px 1026px #fff, 1722px 979px #fff,
    1339px 509px #fff, 1833px 460px #fff, 315px 65px #fff, 496px 1927px #fff,
    1314px 427px #fff, 344px 1046px #fff, 1658px 724px #fff, 1899px 264px #fff,
    1200px 1305px #fff, 1562px 339px #fff, 159px 766px #fff, 1639px 1966px #fff,
    459px 1898px #fff, 944px 763px #fff, 1174px 1056px #fff, 1825px 790px #fff,
    906px 1526px #fff, 1537px 1303px #fff, 79px 1105px #fff, 1318px 672px #fff,
    1232px 61px #fff, 709px 1078px #fff, 1010px 1810px #fff, 777px 1160px #fff,
    1598px 1428px #fff, 815px 684px #fff, 1003px 943px #fff, 1876px 1003px #fff,
    1025px 1529px #fff, 66px 549px #fff, 514px 457px #fff, 262px 1005px #fff,
    812px 1705px #fff, 1163px 1087px #fff, 165px 45px #fff, 677px 1462px #fff,
    580px 1675px #fff, 1848px 1384px #fff, 449px 862px #fff, 1629px 1979px #fff,
    667px 135px #fff, 240px 53px #fff, 1919px 1832px #fff, 696px 1384px #fff,
    1630px 361px #fff, 878px 663px #fff, 1226px 1723px #fff, 765px 686px #fff,
    576px 1647px #fff, 97px 1602px #fff, 1117px 1049px #fff, 1433px 68px #fff,
    1375px 1991px #fff, 1755px 990px #fff, 1483px 801px #fff, 473px 1802px #fff,
    822px 768px #fff, 196px 577px #fff, 516px 504px #fff, 623px 981px #fff,
    1478px 819px #fff, 126px 384px #fff, 584px 1908px #fff, 1549px 521px #fff,
    1866px 1335px #fff, 586px 342px #fff, 1698px 642px #fff, 136px 188px #fff,
    1613px 520px #fff, 937px 326px #fff, 1111px 169px #fff, 229px 229px #fff,
    1357px 20px #fff, 725px 1305px #fff, 23px 1977px #fff, 426px 1945px #fff,
    1628px 1530px #fff, 256px 1295px #fff, 58px 78px #fff, 409px 1145px #fff,
    1607px 767px #fff, 212px 144px #fff, 361px 1890px #fff, 1827px 1451px #fff,
    1875px 645px #fff, 571px 853px #fff, 1302px 301px #fff, 9px 1344px #fff,
    418px 619px #fff, 1941px 90px #fff, 949px 640px #fff, 179px 1783px #fff,
    1104px 360px #fff, 1723px 370px #fff, 1122px 1418px #fff, 1374px 508px #fff,
    1108px 1089px #fff, 1440px 1743px #fff, 462px 1495px #fff, 1187px 265px #fff,
    567px 74px #fff, 557px 542px #fff, 967px 673px #fff, 825px 1971px #fff,
    988px 1260px #fff, 710px 1206px #fff, 538px 1805px #fff, 137px 861px #fff,
    1922px 1313px #fff, 481px 470px #fff, 1224px 316px #fff, 1979px 239px #fff,
    22px 1155px #fff, 1640px 186px #fff, 592px 1709px #fff, 765px 170px #fff,
    129px 1750px #fff, 788px 719px #fff, 181px 1327px #fff, 433px 1455px #fff,
    141px 450px #fff, 1287px 1027px #fff, 1278px 1462px #fff, 688px 1526px #fff,
    463px 1604px #fff, 1232px 297px #fff, 920px 1227px #fff, 1571px 1765px #fff,
    1482px 1316px #fff, 759px 1463px #fff, 950px 1166px #fff, 1532px 1588px #fff,
    608px 267px #fff, 1862px 1943px #fff, 805px 717px #fff, 1803px 1319px #fff,
    1821px 683px #fff, 995px 1958px #fff, 484px 932px #fff, 366px 901px #fff,
    451px 1563px #fff, 1704px 1471px #fff, 1379px 44px #fff, 1778px 472px #fff,
    419px 1806px #fff, 1545px 222px #fff, 1563px 777px #fff, 39px 964px #fff,
    1620px 24px #fff, 1151px 320px #fff, 1940px 1426px #fff, 1555px 1538px #fff,
    1747px 488px #fff, 1348px 300px #fff, 990px 538px #fff, 780px 361px #fff,
    988px 971px #fff, 1973px 1534px #fff, 1542px 1829px #fff, 1557px 216px #fff,
    1404px 641px #fff, 47px 877px #fff, 65px 1738px #fff, 1895px 1798px #fff,
    56px 591px #fff, 536px 906px #fff, 568px 74px #fff, 433px 462px #fff,
    727px 295px #fff, 876px 1878px #fff, 1891px 1946px #fff, 1451px 493px #fff,
    1569px 226px #fff, 879px 1351px #fff, 1529px 43px #fff, 33px 74px #fff,
    1516px 1924px #fff, 878px 323px #fff, 455px 1122px #fff, 1943px 526px #fff,
    1456px 1060px #fff, 1631px 979px #fff, 1819px 1324px #fff,
    1660px 1192px #fff, 1867px 1714px #fff, 1928px 1940px #fff,
    1618px 744px #fff, 979px 357px #fff, 98px 1645px #fff, 1898px 1207px #fff,
    1134px 16px #fff, 1313px 1018px #fff, 717px 812px #fff, 1503px 234px #fff,
    1612px 188px #fff, 29px 459px #fff, 414px 1487px #fff, 1223px 1730px #fff,
    1643px 1188px #fff, 424px 767px #fff, 1692px 1591px #fff, 1265px 367px #fff,
    54px 832px #fff, 410px 804px #fff, 1397px 1242px #fff, 549px 1484px #fff,
    721px 1088px #fff, 472px 1240px #fff, 1927px 514px #fff, 1303px 1310px #fff,
    71px 1276px #fff, 829px 1332px #fff, 84px 1920px #fff, 1088px 375px #fff,
    1659px 736px #fff, 967px 294px #fff, 651px 92px #fff, 1572px 143px #fff,
    1680px 770px #fff, 1873px 1289px #fff, 1983px 821px #fff, 448px 1090px #fff,
    890px 1332px #fff, 836px 867px #fff, 1867px 1213px #fff, 1874px 1574px #fff,
    750px 1063px #fff, 1297px 1971px #fff, 1274px 1015px #fff, 1628px 933px #fff,
    309px 1386px #fff, 299px 1621px #fff, 1973px 526px #fff, 196px 1416px #fff,
    778px 715px #fff, 1993px 1294px #fff, 381px 435px #fff, 1405px 681px #fff,
    1759px 1077px #fff, 1764px 1748px #fff, 168px 470px #fff, 978px 781px #fff,
    110px 1666px #fff, 835px 747px #fff, 112px 95px #fff, 604px 712px #fff,
    1121px 1752px #fff, 393px 1782px #fff, 1869px 830px #fff, 1303px 348px #fff,
    427px 1546px #fff, 761px 718px #fff, 946px 674px #fff, 832px 964px #fff,
    1607px 2000px #fff, 1624px 1296px #fff, 1093px 735px #fff, 1865px 608px #fff,
    933px 1278px #fff, 1402px 547px #fff, 1865px 1211px #fff, 109px 72px #fff,
    249px 1482px #fff, 586px 1933px #fff, 911px 1336px #fff, 697px 853px #fff,
    987px 1797px #fff, 1371px 933px #fff, 492px 1896px #fff, 998px 1866px #fff,
    518px 31px #fff, 1873px 372px #fff, 1025px 1308px #fff, 1478px 965px #fff,
    1934px 934px #fff, 1048px 1262px #fff, 1839px 40px #fff, 1399px 732px #fff,
    735px 416px #fff, 621px 394px #fff, 788px 1802px #fff, 1918px 307px #fff,
    432px 1845px #fff, 616px 481px #fff, 921px 798px #fff, 354px 597px #fff,
    1622px 214px #fff, 1349px 1983px #fff, 1033px 1622px #fff, 1198px 407px #fff,
    1239px 1449px #fff, 1278px 1978px #fff, 426px 1264px #fff, 507px 1341px #fff,
    1956px 818px #fff, 1041px 277px #fff, 1371px 639px #fff, 1224px 419px #fff,
    211px 1106px #fff, 847px 656px #fff, 534px 1891px #fff, 1289px 823px #fff,
    906px 482px #fff, 347px 1837px #fff, 1246px 1462px #fff, 915px 1858px #fff,
    559px 1320px #fff, 77px 1555px #fff, 845px 1743px #fff, 313px 1414px #fff,
    188px 252px #fff, 509px 637px #fff, 374px 142px #fff, 1397px 474px #fff,
    458px 1197px #fff, 292px 619px #fff, 1749px 14px #fff, 1638px 24px #fff,
    563px 1752px #fff, 1940px 1065px #fff, 1145px 1030px #fff, 894px 1470px #fff,
    444px 32px #fff, 1341px 1136px #fff, 1941px 412px #fff, 1328px 785px #fff,
    161px 1740px #fff, 948px 829px #fff, 933px 823px #fff, 1709px 507px #fff,
    1366px 1821px #fff, 720px 731px #fff, 162px 682px #fff, 1684px 882px #fff,
    134px 497px #fff, 1659px 1701px #fff, 1186px 446px #fff, 911px 1435px #fff,
    1814px 1028px #fff, 1234px 1520px #fff, 1186px 23px #fff, 318px 87px #fff,
    1179px 837px #fff, 1071px 46px #fff, 1125px 1862px #fff, 94px 261px #fff,
    1574px 282px #fff, 1039px 815px #fff, 1776px 1472px #fff, 867px 473px #fff,
    901px 215px #fff, 862px 630px #fff, 1480px 1673px #fff, 411px 1896px #fff,
    1335px 944px #fff, 148px 1235px #fff, 57px 140px #fff, 447px 651px #fff,
    1414px 1651px #fff, 209px 1770px #fff, 1800px 1590px #fff, 1304px 1px #fff,
    279px 771px #fff, 1770px 1398px #fff, 724px 1201px #fff, 245px 1145px #fff,
    172px 1951px #fff, 284px 236px #fff, 1905px 1307px #fff, 1948px 574px #fff,
    283px 669px #fff, 247px 384px #fff, 224px 619px #fff, 128px 772px #fff,
    1698px 1405px #fff, 830px 505px #fff, 1938px 397px #fff, 1772px 1001px #fff,
    1454px 808px #fff, 304px 561px #fff, 1321px 966px #fff, 735px 1368px #fff,
    894px 345px #fff, 1217px 1997px #fff, 892px 1342px #fff, 353px 379px #fff,
    1382px 1156px #fff, 164px 1239px #fff, 1268px 1859px #fff,
    1385px 1721px #fff, 16px 283px #fff, 1819px 200px #fff, 660px 1111px #fff,
    1679px 1728px #fff, 463px 596px #fff, 217px 1834px #fff, 1879px 538px #fff,
    304px 906px #fff, 1327px 1347px #fff, 1226px 1579px #fff, 1786px 1616px #fff,
    1234px 1982px #fff, 1868px 1862px #fff, 814px 948px #fff, 178px 1837px #fff,
    571px 1701px #fff, 106px 566px #fff, 270px 925px #fff, 1417px 248px #fff,
    609px 1551px #fff, 992px 1825px #fff, 1515px 1999px #fff, 1167px 914px #fff,
    1698px 490px #fff, 189px 1463px #fff, 928px 612px #fff, 1714px 803px #fff,
    535px 402px #fff, 1000px 379px #fff, 1610px 574px #fff, 1882px 1155px #fff,
    1425px 1514px #fff, 417px 1987px #fff, 1681px 1059px #fff, 841px 762px #fff,
    1886px 1098px #fff, 1785px 236px #fff, 1950px 950px #fff, 444px 1937px #fff,
    1364px 540px #fff, 1971px 225px #fff, 1624px 868px #fff, 869px 640px #fff,
    1637px 559px #fff, 20px 823px #fff, 409px 177px #fff, 1804px 1626px #fff,
    388px 527px #fff, 1385px 1734px #fff, 988px 1310px #fff, 443px 599px #fff,
    1780px 434px #fff, 654px 419px #fff, 268px 1424px #fff, 1971px 40px #fff,
    360px 1834px #fff, 875px 1930px #fff, 1866px 1885px #fff, 453px 1670px #fff,
    1696px 1337px #fff, 604px 1887px #fff, 1405px 769px #fff, 1546px 897px #fff,
    595px 1975px #fff, 32px 1765px #fff, 896px 1150px #fff, 1818px 95px #fff,
    444px 49px #fff, 589px 1796px #fff, 764px 1965px #fff, 920px 1803px #fff,
    403px 1997px #fff, 833px 1282px #fff, 1127px 1770px #fff, 1810px 77px #fff,
    1214px 1102px #fff, 364px 401px #fff, 1139px 1191px #fff, 916px 1907px #fff,
    870px 290px #fff, 688px 678px #fff, 1523px 34px #fff, 1265px 1082px #fff,
    1394px 1080px #fff, 1787px 1738px #fff, 1682px 755px #fff, 1955px 832px #fff,
    546px 1577px #fff, 1062px 1561px #fff, 344px 826px #fff, 1442px 782px #fff,
    467px 1477px #fff, 879px 1439px #fff, 1672px 268px #fff, 1317px 1355px #fff,
    1980px 1965px #fff, 688px 1465px #fff, 1131px 872px #fff, 1301px 1656px #fff,
    974px 583px #fff, 1613px 1467px #fff, 1976px 1995px #fff, 1377px 760px #fff,
    1367px 387px #fff, 1880px 191px #fff, 711px 876px #fff, 539px 152px #fff,
    545px 1809px #fff, 920px 970px #fff, 1154px 1355px #fff, 1968px 94px #fff,
    1703px 490px #fff, 380px 146px #fff, 1561px 785px #fff, 1930px 1385px #fff,
    519px 1091px #fff, 269px 570px #fff, 109px 1326px #fff, 1476px 969px #fff,
    1999px 1885px #fff, 341px 1238px #fff, 1105px 1076px #fff, 596px 88px #fff,
    937px 492px #fff, 1339px 1673px #fff, 1967px 762px #fff, 65px 952px #fff,
    111px 93px #fff, 1011px 1684px #fff, 377px 1430px #fff, 1011px 386px #fff,
    1162px 421px #fff, 196px 617px #fff, 1407px 1141px #fff, 1562px 572px #fff,
    316px 690px #fff, 1600px 1980px #fff, 1545px 1254px #fff, 680px 1120px #fff,
    575px 1284px #fff, 179px 1470px #fff, 1496px 1506px #fff, 977px 1376px #fff,
    1282px 708px #fff, 408px 1427px #fff, 1173px 1597px #fff, 1120px 1755px #fff,
    974px 520px #fff, 979px 384px #fff, 622px 1116px #fff, 1307px 866px #fff,
    1188px 1596px #fff, 858px 1947px #fff, 861px 1373px #fff, 857px 43px #fff,
    1878px 499px #fff, 1297px 535px #fff, 870px 1286px #fff, 1452px 448px #fff,
    906px 72px #fff, 1450px 872px #fff, 1607px 1755px #fff, 1071px 1959px #fff,
    976px 879px #fff, 1435px 284px #fff, 1601px 496px #fff, 671px 1713px #fff,
    356px 1148px #fff, 837px 867px #fff, 246px 858px #fff, 1031px 1594px #fff,
    571px 214px #fff, 1012px 1329px #fff, 1566px 1142px #fff, 219px 1376px #fff,
    1580px 1454px #fff, 690px 1037px #fff, 1940px 756px #fff, 1755px 850px #fff,
    1037px 363px #fff, 25px 515px #fff, 806px 1571px #fff, 1266px 1398px #fff,
    695px 791px #fff, 1838px 950px #fff, 1578px 27px #fff, 1692px 1581px #fff,
    1817px 1040px #fff, 1781px 238px #fff, 1052px 895px #fff, 398px 440px #fff,
    1643px 325px #fff, 79px 848px #fff, 1295px 326px #fff, 293px 81px #fff,
    1202px 1580px #fff, 1441px 100px #fff, 1295px 1465px #fff,
    1132px 1275px #fff, 774px 704px #fff, 1109px 1546px #fff, 557px 1390px #fff,
    1253px 1224px #fff, 1063px 1618px #fff, 1793px 440px #fff, 890px 1988px #fff,
    814px 424px #fff, 1390px 1280px #fff, 1127px 907px #fff, 1044px 1368px #fff,
    1317px 876px #fff, 762px 638px #fff, 965px 747px #fff, 755px 1945px #fff,
    706px 414px #fff, 1842px 1673px #fff, 1158px 1336px #fff, 989px 1955px #fff,
    1857px 89px #fff, 1659px 343px #fff, 509px 1385px #fff, 1620px 758px #fff,
    1958px 1064px #fff, 178px 341px #fff, 1500px 808px #fff, 20px 1646px #fff,
    1572px 870px #fff, 1821px 1377px #fff, 328px 1965px #fff, 903px 616px #fff,
    1731px 1599px #fff, 267px 856px #fff;
  /* animation: animStar 50s linear infinite;
       animation: blink 2s linear infinite;
            -webkit-animation: blink 2s linear infinite;
            -moz-animation: blink 2s linear infinite;
            -ms-animation: blink 2s linear infinite;
            -o-animation: blink 2s linear infinite; */
}

.stars:after {
  content: ' ';
  position: absolute;
  /* top: 2000px; */
  width: 1px;
  height: 1px;
  background: transparent;
  box-shadow: 1407px 511px #fff, 1611px 119px #fff, 1686px 956px #fff,
    1163px 1929px #fff, 912px 1242px #fff, 490px 469px #fff, 869px 425px #fff,
    1447px 891px #fff, 422px 1960px #fff, 517px 1995px #fff, 738px 171px #fff,
    1328px 1668px #fff, 874px 1490px #fff, 83px 81px #fff, 632px 98px #fff,
    1518px 1764px #fff, 636px 596px #fff, 1178px 131px #fff, 278px 1179px #fff,
    1898px 1951px #fff, 1787px 326px #fff, 186px 1588px #fff, 552px 1942px #fff,
    1929px 1300px #fff, 802px 681px #fff, 430px 1711px #fff, 1192px 308px #fff,
    123px 1604px #fff, 880px 169px #fff, 1400px 632px #fff, 500px 1165px #fff,
    288px 1208px #fff, 319px 1419px #fff, 1170px 980px #fff, 1608px 784px #fff,
    1735px 1276px #fff, 966px 1534px #fff, 654px 783px #fff, 1366px 964px #fff,
    1213px 60px #fff, 302px 1509px #fff, 845px 714px #fff, 524px 323px #fff,
    1538px 1399px #fff, 394px 619px #fff, 680px 26px #fff, 353px 776px #fff,
    1826px 1450px #fff, 1909px 1452px #fff, 1014px 1315px #fff,
    1883px 1474px #fff, 766px 1742px #fff, 1693px 658px #fff, 1186px 302px #fff,
    376px 1575px #fff, 712px 1739px #fff, 1627px 299px #fff, 482px 224px #fff,
    1379px 510px #fff, 1543px 1602px #fff, 45px 606px #fff, 827px 1336px #fff,
    224px 1939px #fff, 1098px 1342px #fff, 813px 1553px #fff, 825px 419px #fff,
    519px 894px #fff, 1406px 797px #fff, 1341px 274px #fff, 1787px 903px #fff,
    1701px 1483px #fff, 1108px 232px #fff, 1599px 1409px #fff, 659px 870px #fff,
    1538px 335px #fff, 632px 1855px #fff, 154px 1026px #fff, 1722px 979px #fff,
    1339px 509px #fff, 1833px 460px #fff, 315px 65px #fff, 496px 1927px #fff,
    1314px 427px #fff, 344px 1046px #fff, 1658px 724px #fff, 1899px 264px #fff,
    1200px 1305px #fff, 1562px 339px #fff, 159px 766px #fff, 1639px 1966px #fff,
    459px 1898px #fff, 944px 763px #fff, 1174px 1056px #fff, 1825px 790px #fff,
    906px 1526px #fff, 1537px 1303px #fff, 79px 1105px #fff, 1318px 672px #fff,
    1232px 61px #fff, 709px 1078px #fff, 1010px 1810px #fff, 777px 1160px #fff,
    1598px 1428px #fff, 815px 684px #fff, 1003px 943px #fff, 1876px 1003px #fff,
    1025px 1529px #fff, 66px 549px #fff, 514px 457px #fff, 262px 1005px #fff,
    812px 1705px #fff, 1163px 1087px #fff, 165px 45px #fff, 677px 1462px #fff,
    580px 1675px #fff, 1848px 1384px #fff, 449px 862px #fff, 1629px 1979px #fff,
    667px 135px #fff, 240px 53px #fff, 1919px 1832px #fff, 696px 1384px #fff,
    1630px 361px #fff, 878px 663px #fff, 1226px 1723px #fff, 765px 686px #fff,
    576px 1647px #fff, 97px 1602px #fff, 1117px 1049px #fff, 1433px 68px #fff,
    1375px 1991px #fff, 1755px 990px #fff, 1483px 801px #fff, 473px 1802px #fff,
    822px 768px #fff, 196px 577px #fff, 516px 504px #fff, 623px 981px #fff,
    1478px 819px #fff, 126px 384px #fff, 584px 1908px #fff, 1549px 521px #fff,
    1866px 1335px #fff, 586px 342px #fff, 1698px 642px #fff, 136px 188px #fff,
    1613px 520px #fff, 937px 326px #fff, 1111px 169px #fff, 229px 229px #fff,
    1357px 20px #fff, 725px 1305px #fff, 23px 1977px #fff, 426px 1945px #fff,
    1628px 1530px #fff, 256px 1295px #fff, 58px 78px #fff, 409px 1145px #fff,
    1607px 767px #fff, 212px 144px #fff, 361px 1890px #fff, 1827px 1451px #fff,
    1875px 645px #fff, 571px 853px #fff, 1302px 301px #fff, 9px 1344px #fff,
    418px 619px #fff, 1941px 90px #fff, 949px 640px #fff, 179px 1783px #fff,
    1104px 360px #fff, 1723px 370px #fff, 1122px 1418px #fff, 1374px 508px #fff,
    1108px 1089px #fff, 1440px 1743px #fff, 462px 1495px #fff, 1187px 265px #fff,
    567px 74px #fff, 557px 542px #fff, 967px 673px #fff, 825px 1971px #fff,
    988px 1260px #fff, 710px 1206px #fff, 538px 1805px #fff, 137px 861px #fff,
    1922px 1313px #fff, 481px 470px #fff, 1224px 316px #fff, 1979px 239px #fff,
    22px 1155px #fff, 1640px 186px #fff, 592px 1709px #fff, 765px 170px #fff,
    129px 1750px #fff, 788px 719px #fff, 181px 1327px #fff, 433px 1455px #fff,
    141px 450px #fff, 1287px 1027px #fff, 1278px 1462px #fff, 688px 1526px #fff,
    463px 1604px #fff, 1232px 297px #fff, 920px 1227px #fff, 1571px 1765px #fff,
    1482px 1316px #fff, 759px 1463px #fff, 950px 1166px #fff, 1532px 1588px #fff,
    608px 267px #fff, 1862px 1943px #fff, 805px 717px #fff, 1803px 1319px #fff,
    1821px 683px #fff, 995px 1958px #fff, 484px 932px #fff, 366px 901px #fff,
    451px 1563px #fff, 1704px 1471px #fff, 1379px 44px #fff, 1778px 472px #fff,
    419px 1806px #fff, 1545px 222px #fff, 1563px 777px #fff, 39px 964px #fff,
    1620px 24px #fff, 1151px 320px #fff, 1940px 1426px #fff, 1555px 1538px #fff,
    1747px 488px #fff, 1348px 300px #fff, 990px 538px #fff, 780px 361px #fff,
    988px 971px #fff, 1973px 1534px #fff, 1542px 1829px #fff, 1557px 216px #fff,
    1404px 641px #fff, 47px 877px #fff, 65px 1738px #fff, 1895px 1798px #fff,
    56px 591px #fff, 536px 906px #fff, 568px 74px #fff, 433px 462px #fff,
    727px 295px #fff, 876px 1878px #fff, 1891px 1946px #fff, 1451px 493px #fff,
    1569px 226px #fff, 879px 1351px #fff, 1529px 43px #fff, 33px 74px #fff,
    1516px 1924px #fff, 878px 323px #fff, 455px 1122px #fff, 1943px 526px #fff,
    1456px 1060px #fff, 1631px 979px #fff, 1819px 1324px #fff,
    1660px 1192px #fff, 1867px 1714px #fff, 1928px 1940px #fff,
    1618px 744px #fff, 979px 357px #fff, 98px 1645px #fff, 1898px 1207px #fff,
    1134px 16px #fff, 1313px 1018px #fff, 717px 812px #fff, 1503px 234px #fff,
    1612px 188px #fff, 29px 459px #fff, 414px 1487px #fff, 1223px 1730px #fff,
    1643px 1188px #fff, 424px 767px #fff, 1692px 1591px #fff, 1265px 367px #fff,
    54px 832px #fff, 410px 804px #fff, 1397px 1242px #fff, 549px 1484px #fff,
    721px 1088px #fff, 472px 1240px #fff, 1927px 514px #fff, 1303px 1310px #fff,
    71px 1276px #fff, 829px 1332px #fff, 84px 1920px #fff, 1088px 375px #fff,
    1659px 736px #fff, 967px 294px #fff, 651px 92px #fff, 1572px 143px #fff,
    1680px 770px #fff, 1873px 1289px #fff, 1983px 821px #fff, 448px 1090px #fff,
    890px 1332px #fff, 836px 867px #fff, 1867px 1213px #fff, 1874px 1574px #fff,
    750px 1063px #fff, 1297px 1971px #fff, 1274px 1015px #fff, 1628px 933px #fff,
    309px 1386px #fff, 299px 1621px #fff, 1973px 526px #fff, 196px 1416px #fff,
    778px 715px #fff, 1993px 1294px #fff, 381px 435px #fff, 1405px 681px #fff,
    1759px 1077px #fff, 1764px 1748px #fff, 168px 470px #fff, 978px 781px #fff,
    110px 1666px #fff, 835px 747px #fff, 112px 95px #fff, 604px 712px #fff,
    1121px 1752px #fff, 393px 1782px #fff, 1869px 830px #fff, 1303px 348px #fff,
    427px 1546px #fff, 761px 718px #fff, 946px 674px #fff, 832px 964px #fff,
    1607px 2000px #fff, 1624px 1296px #fff, 1093px 735px #fff, 1865px 608px #fff,
    933px 1278px #fff, 1402px 547px #fff, 1865px 1211px #fff, 109px 72px #fff,
    249px 1482px #fff, 586px 1933px #fff, 911px 1336px #fff, 697px 853px #fff,
    987px 1797px #fff, 1371px 933px #fff, 492px 1896px #fff, 998px 1866px #fff,
    518px 31px #fff, 1873px 372px #fff, 1025px 1308px #fff, 1478px 965px #fff,
    1934px 934px #fff, 1048px 1262px #fff, 1839px 40px #fff, 1399px 732px #fff,
    735px 416px #fff, 621px 394px #fff, 788px 1802px #fff, 1918px 307px #fff,
    432px 1845px #fff, 616px 481px #fff, 921px 798px #fff, 354px 597px #fff,
    1622px 214px #fff, 1349px 1983px #fff, 1033px 1622px #fff, 1198px 407px #fff,
    1239px 1449px #fff, 1278px 1978px #fff, 426px 1264px #fff, 507px 1341px #fff,
    1956px 818px #fff, 1041px 277px #fff, 1371px 639px #fff, 1224px 419px #fff,
    211px 1106px #fff, 847px 656px #fff, 534px 1891px #fff, 1289px 823px #fff,
    906px 482px #fff, 347px 1837px #fff, 1246px 1462px #fff, 915px 1858px #fff,
    559px 1320px #fff, 77px 1555px #fff, 845px 1743px #fff, 313px 1414px #fff,
    188px 252px #fff, 509px 637px #fff, 374px 142px #fff, 1397px 474px #fff,
    458px 1197px #fff, 292px 619px #fff, 1749px 14px #fff, 1638px 24px #fff,
    563px 1752px #fff, 1940px 1065px #fff, 1145px 1030px #fff, 894px 1470px #fff,
    444px 32px #fff, 1341px 1136px #fff, 1941px 412px #fff, 1328px 785px #fff,
    161px 1740px #fff, 948px 829px #fff, 933px 823px #fff, 1709px 507px #fff,
    1366px 1821px #fff, 720px 731px #fff, 162px 682px #fff, 1684px 882px #fff,
    134px 497px #fff, 1659px 1701px #fff, 1186px 446px #fff, 911px 1435px #fff,
    1814px 1028px #fff, 1234px 1520px #fff, 1186px 23px #fff, 318px 87px #fff,
    1179px 837px #fff, 1071px 46px #fff, 1125px 1862px #fff, 94px 261px #fff,
    1574px 282px #fff, 1039px 815px #fff, 1776px 1472px #fff, 867px 473px #fff,
    901px 215px #fff, 862px 630px #fff, 1480px 1673px #fff, 411px 1896px #fff,
    1335px 944px #fff, 148px 1235px #fff, 57px 140px #fff, 447px 651px #fff,
    1414px 1651px #fff, 209px 1770px #fff, 1800px 1590px #fff, 1304px 1px #fff,
    279px 771px #fff, 1770px 1398px #fff, 724px 1201px #fff, 245px 1145px #fff,
    172px 1951px #fff, 284px 236px #fff, 1905px 1307px #fff, 1948px 574px #fff,
    283px 669px #fff, 247px 384px #fff, 224px 619px #fff, 128px 772px #fff,
    1698px 1405px #fff, 830px 505px #fff, 1938px 397px #fff, 1772px 1001px #fff,
    1454px 808px #fff, 304px 561px #fff, 1321px 966px #fff, 735px 1368px #fff,
    894px 345px #fff, 1217px 1997px #fff, 892px 1342px #fff, 353px 379px #fff,
    1382px 1156px #fff, 164px 1239px #fff, 1268px 1859px #fff,
    1385px 1721px #fff, 16px 283px #fff, 1819px 200px #fff, 660px 1111px #fff,
    1679px 1728px #fff, 463px 596px #fff, 217px 1834px #fff, 1879px 538px #fff,
    304px 906px #fff, 1327px 1347px #fff, 1226px 1579px #fff, 1786px 1616px #fff,
    1234px 1982px #fff, 1868px 1862px #fff, 814px 948px #fff, 178px 1837px #fff,
    571px 1701px #fff, 106px 566px #fff, 270px 925px #fff, 1417px 248px #fff,
    609px 1551px #fff, 992px 1825px #fff, 1515px 1999px #fff, 1167px 914px #fff,
    1698px 490px #fff, 189px 1463px #fff, 928px 612px #fff, 1714px 803px #fff,
    535px 402px #fff, 1000px 379px #fff, 1610px 574px #fff, 1882px 1155px #fff,
    1425px 1514px #fff, 417px 1987px #fff, 1681px 1059px #fff, 841px 762px #fff,
    1886px 1098px #fff, 1785px 236px #fff, 1950px 950px #fff, 444px 1937px #fff,
    1364px 540px #fff, 1971px 225px #fff, 1624px 868px #fff, 869px 640px #fff,
    1637px 559px #fff, 20px 823px #fff, 409px 177px #fff, 1804px 1626px #fff,
    388px 527px #fff, 1385px 1734px #fff, 988px 1310px #fff, 443px 599px #fff,
    1780px 434px #fff, 654px 419px #fff, 268px 1424px #fff, 1971px 40px #fff,
    360px 1834px #fff, 875px 1930px #fff, 1866px 1885px #fff, 453px 1670px #fff,
    1696px 1337px #fff, 604px 1887px #fff, 1405px 769px #fff, 1546px 897px #fff,
    595px 1975px #fff, 32px 1765px #fff, 896px 1150px #fff, 1818px 95px #fff,
    444px 49px #fff, 589px 1796px #fff, 764px 1965px #fff, 920px 1803px #fff,
    403px 1997px #fff, 833px 1282px #fff, 1127px 1770px #fff, 1810px 77px #fff,
    1214px 1102px #fff, 364px 401px #fff, 1139px 1191px #fff, 916px 1907px #fff,
    870px 290px #fff, 688px 678px #fff, 1523px 34px #fff, 1265px 1082px #fff,
    1394px 1080px #fff, 1787px 1738px #fff, 1682px 755px #fff, 1955px 832px #fff,
    546px 1577px #fff, 1062px 1561px #fff, 344px 826px #fff, 1442px 782px #fff,
    467px 1477px #fff, 879px 1439px #fff, 1672px 268px #fff, 1317px 1355px #fff,
    1980px 1965px #fff, 688px 1465px #fff, 1131px 872px #fff, 1301px 1656px #fff,
    974px 583px #fff, 1613px 1467px #fff, 1976px 1995px #fff, 1377px 760px #fff,
    1367px 387px #fff, 1880px 191px #fff, 711px 876px #fff, 539px 152px #fff,
    545px 1809px #fff, 920px 970px #fff, 1154px 1355px #fff, 1968px 94px #fff,
    1703px 490px #fff, 380px 146px #fff, 1561px 785px #fff, 1930px 1385px #fff,
    519px 1091px #fff, 269px 570px #fff, 109px 1326px #fff, 1476px 969px #fff,
    1999px 1885px #fff, 341px 1238px #fff, 1105px 1076px #fff, 596px 88px #fff,
    937px 492px #fff, 1339px 1673px #fff, 1967px 762px #fff, 65px 952px #fff,
    111px 93px #fff, 1011px 1684px #fff, 377px 1430px #fff, 1011px 386px #fff,
    1162px 421px #fff, 196px 617px #fff, 1407px 1141px #fff, 1562px 572px #fff,
    316px 690px #fff, 1600px 1980px #fff, 1545px 1254px #fff, 680px 1120px #fff,
    575px 1284px #fff, 179px 1470px #fff, 1496px 1506px #fff, 977px 1376px #fff,
    1282px 708px #fff, 408px 1427px #fff, 1173px 1597px #fff, 1120px 1755px #fff,
    974px 520px #fff, 979px 384px #fff, 622px 1116px #fff, 1307px 866px #fff,
    1188px 1596px #fff, 858px 1947px #fff, 861px 1373px #fff, 857px 43px #fff,
    1878px 499px #fff, 1297px 535px #fff, 870px 1286px #fff, 1452px 448px #fff,
    906px 72px #fff, 1450px 872px #fff, 1607px 1755px #fff, 1071px 1959px #fff,
    976px 879px #fff, 1435px 284px #fff, 1601px 496px #fff, 671px 1713px #fff,
    356px 1148px #fff, 837px 867px #fff, 246px 858px #fff, 1031px 1594px #fff,
    571px 214px #fff, 1012px 1329px #fff, 1566px 1142px #fff, 219px 1376px #fff,
    1580px 1454px #fff, 690px 1037px #fff, 1940px 756px #fff, 1755px 850px #fff,
    1037px 363px #fff, 25px 515px #fff, 806px 1571px #fff, 1266px 1398px #fff,
    695px 791px #fff, 1838px 950px #fff, 1578px 27px #fff, 1692px 1581px #fff,
    1817px 1040px #fff, 1781px 238px #fff, 1052px 895px #fff, 398px 440px #fff,
    1643px 325px #fff, 79px 848px #fff, 1295px 326px #fff, 293px 81px #fff,
    1202px 1580px #fff, 1441px 100px #fff, 1295px 1465px #fff,
    1132px 1275px #fff, 774px 704px #fff, 1109px 1546px #fff, 557px 1390px #fff,
    1253px 1224px #fff, 1063px 1618px #fff, 1793px 440px #fff, 890px 1988px #fff,
    814px 424px #fff, 1390px 1280px #fff, 1127px 907px #fff, 1044px 1368px #fff,
    1317px 876px #fff, 762px 638px #fff, 965px 747px #fff, 755px 1945px #fff,
    706px 414px #fff, 1842px 1673px #fff, 1158px 1336px #fff, 989px 1955px #fff,
    1857px 89px #fff, 1659px 343px #fff, 509px 1385px #fff, 1620px 758px #fff,
    1958px 1064px #fff, 178px 341px #fff, 1500px 808px #fff, 20px 1646px #fff,
    1572px 870px #fff, 1821px 1377px #fff, 328px 1965px #fff, 903px 616px #fff,
    1731px 1599px #fff, 267px 856px #fff;
}

#stars2 {
  width: 2px;
  height: 2px;
  background: transparent;
  box-shadow: 921px 1554px #fff, 1944px 550px #fff, 1696px 1632px #fff,
    16px 1899px #fff, 1894px 130px #fff, 77px 262px #fff, 22px 1159px #fff,
    933px 1206px #fff, 1660px 482px #fff, 1067px 1154px #fff, 468px 625px #fff,
    1408px 1687px #fff, 153px 1200px #fff, 887px 1966px #fff, 1260px 514px #fff,
    1167px 1158px #fff, 790px 553px #fff, 1103px 758px #fff, 226px 1028px #fff,
    1340px 1760px #fff, 1712px 528px #fff, 114px 1693px #fff, 185px 572px #fff,
    1566px 1793px #fff, 317px 1501px #fff, 846px 530px #fff, 1585px 1437px #fff,
    1335px 1009px #fff, 1768px 436px #fff, 1131px 666px #fff, 27px 1543px #fff,
    1778px 1861px #fff, 1496px 30px #fff, 1359px 1226px #fff, 416px 135px #fff,
    1675px 673px #fff, 296px 524px #fff, 432px 1822px #fff, 1995px 416px #fff,
    1206px 1846px #fff, 542px 603px #fff, 1811px 1083px #fff, 1125px 1900px #fff,
    4px 1410px #fff, 665px 1674px #fff, 982px 365px #fff, 809px 534px #fff,
    116px 1381px #fff, 727px 439px #fff, 1674px 1407px #fff, 976px 1762px #fff,
    1585px 28px #fff, 1916px 624px #fff, 1716px 1118px #fff, 1022px 177px #fff,
    807px 619px #fff, 1657px 338px #fff, 1608px 1259px #fff, 405px 1890px #fff,
    433px 1978px #fff, 1457px 1495px #fff, 175px 989px #fff, 850px 1044px #fff,
    170px 444px #fff, 1623px 71px #fff, 977px 1319px #fff, 440px 464px #fff,
    51px 1209px #fff, 783px 1274px #fff, 1296px 244px #fff, 1260px 94px #fff,
    652px 905px #fff, 805px 1307px #fff, 947px 822px #fff, 384px 268px #fff,
    1856px 1782px #fff, 459px 1844px #fff, 1679px 473px #fff, 673px 1832px #fff,
    96px 345px #fff, 1268px 428px #fff, 788px 1138px #fff, 1242px 867px #fff,
    652px 831px #fff, 993px 1706px #fff, 1337px 64px #fff, 1092px 624px #fff,
    674px 1344px #fff, 1036px 405px #fff, 996px 1371px #fff, 1906px 1410px #fff,
    1285px 1079px #fff, 1756px 583px #fff, 404px 380px #fff, 1739px 1620px #fff,
    1253px 372px #fff, 520px 620px #fff, 1842px 852px #fff, 490px 387px #fff,
    1251px 143px #fff, 1814px 537px #fff, 1405px 623px #fff, 1236px 1186px #fff,
    1286px 896px #fff, 1626px 990px #fff, 31px 1067px #fff, 1288px 939px #fff,
    763px 338px #fff, 713px 1515px #fff, 859px 1621px #fff, 1720px 1984px #fff,
    796px 1743px #fff, 1439px 1587px #fff, 965px 412px #fff, 775px 1168px #fff,
    1192px 956px #fff, 368px 1075px #fff, 1484px 1154px #fff, 1784px 547px #fff,
    815px 675px #fff, 1387px 890px #fff, 1665px 1733px #fff, 1948px 429px #fff,
    1665px 92px #fff, 1806px 919px #fff, 1712px 494px #fff, 577px 1922px #fff,
    820px 1228px #fff, 678px 1745px #fff, 1421px 586px #fff, 788px 208px #fff,
    380px 250px #fff, 748px 977px #fff, 1637px 337px #fff, 851px 1514px #fff,
    1487px 1410px #fff, 1776px 710px #fff, 544px 453px #fff, 1707px 1932px #fff,
    1121px 1642px #fff, 1227px 391px #fff, 583px 833px #fff, 658px 278px #fff,
    345px 1388px #fff, 1529px 1419px #fff, 233px 1008px #fff, 892px 943px #fff,
    1431px 1091px #fff, 1524px 316px #fff, 1547px 192px #fff, 976px 36px #fff,
    1648px 1053px #fff, 1833px 1572px #fff, 1677px 936px #fff, 589px 1755px #fff,
    978px 1875px #fff, 1508px 412px #fff, 1242px 439px #fff, 1263px 40px #fff,
    1427px 1736px #fff, 639px 906px #fff, 1349px 373px #fff, 1055px 969px #fff,
    602px 95px #fff, 224px 1805px #fff, 1129px 837px #fff, 1110px 1358px #fff,
    1067px 1752px #fff, 391px 1389px #fff, 885px 1979px #fff, 1188px 414px #fff,
    1931px 325px #fff, 1853px 1918px #fff, 636px 1313px #fff, 1236px 1913px #fff,
    1801px 780px #fff, 633px 529px #fff, 1500px 33px #fff, 1387px 1045px #fff,
    832px 1281px #fff, 1880px 1845px #fff, 1477px 1096px #fff, 1457px 698px #fff,
    1658px 1049px #fff, 1957px 1151px #fff, 1561px 1593px #fff, 627px 250px #fff,
    975px 1575px #fff, 68px 998px #fff, 951px 85px #fff, 280px 431px #fff,
    1683px 1745px #fff, 322px 778px #fff, 841px 888px #fff, 1895px 1883px #fff,
    700px 568px #fff, 1846px 442px #fff, 91px 1650px #fff, 970px 917px #fff,
    1585px 452px #fff;
  /* animation: animStar 100s linear infinite; */
}

#stars2:after {
  content: ' ';
  position: absolute;
  /* top: 2000px; */
  width: 2px;
  height: 2px;
  background: transparent;
  box-shadow: 921px 1554px #fff, 1944px 550px #fff, 1696px 1632px #fff,
    16px 1899px #fff, 1894px 130px #fff, 77px 262px #fff, 22px 1159px #fff,
    933px 1206px #fff, 1660px 482px #fff, 1067px 1154px #fff, 468px 625px #fff,
    1408px 1687px #fff, 153px 1200px #fff, 887px 1966px #fff, 1260px 514px #fff,
    1167px 1158px #fff, 790px 553px #fff, 1103px 758px #fff, 226px 1028px #fff,
    1340px 1760px #fff, 1712px 528px #fff, 114px 1693px #fff, 185px 572px #fff,
    1566px 1793px #fff, 317px 1501px #fff, 846px 530px #fff, 1585px 1437px #fff,
    1335px 1009px #fff, 1768px 436px #fff, 1131px 666px #fff, 27px 1543px #fff,
    1778px 1861px #fff, 1496px 30px #fff, 1359px 1226px #fff, 416px 135px #fff,
    1675px 673px #fff, 296px 524px #fff, 432px 1822px #fff, 1995px 416px #fff,
    1206px 1846px #fff, 542px 603px #fff, 1811px 1083px #fff, 1125px 1900px #fff,
    4px 1410px #fff, 665px 1674px #fff, 982px 365px #fff, 809px 534px #fff,
    116px 1381px #fff, 727px 439px #fff, 1674px 1407px #fff, 976px 1762px #fff,
    1585px 28px #fff, 1916px 624px #fff, 1716px 1118px #fff, 1022px 177px #fff,
    807px 619px #fff, 1657px 338px #fff, 1608px 1259px #fff, 405px 1890px #fff,
    433px 1978px #fff, 1457px 1495px #fff, 175px 989px #fff, 850px 1044px #fff,
    170px 444px #fff, 1623px 71px #fff, 977px 1319px #fff, 440px 464px #fff,
    51px 1209px #fff, 783px 1274px #fff, 1296px 244px #fff, 1260px 94px #fff,
    652px 905px #fff, 805px 1307px #fff, 947px 822px #fff, 384px 268px #fff,
    1856px 1782px #fff, 459px 1844px #fff, 1679px 473px #fff, 673px 1832px #fff,
    96px 345px #fff, 1268px 428px #fff, 788px 1138px #fff, 1242px 867px #fff,
    652px 831px #fff, 993px 1706px #fff, 1337px 64px #fff, 1092px 624px #fff,
    674px 1344px #fff, 1036px 405px #fff, 996px 1371px #fff, 1906px 1410px #fff,
    1285px 1079px #fff, 1756px 583px #fff, 404px 380px #fff, 1739px 1620px #fff,
    1253px 372px #fff, 520px 620px #fff, 1842px 852px #fff, 490px 387px #fff,
    1251px 143px #fff, 1814px 537px #fff, 1405px 623px #fff, 1236px 1186px #fff,
    1286px 896px #fff, 1626px 990px #fff, 31px 1067px #fff, 1288px 939px #fff,
    763px 338px #fff, 713px 1515px #fff, 859px 1621px #fff, 1720px 1984px #fff,
    796px 1743px #fff, 1439px 1587px #fff, 965px 412px #fff, 775px 1168px #fff,
    1192px 956px #fff, 368px 1075px #fff, 1484px 1154px #fff, 1784px 547px #fff,
    815px 675px #fff, 1387px 890px #fff, 1665px 1733px #fff, 1948px 429px #fff,
    1665px 92px #fff, 1806px 919px #fff, 1712px 494px #fff, 577px 1922px #fff,
    820px 1228px #fff, 678px 1745px #fff, 1421px 586px #fff, 788px 208px #fff,
    380px 250px #fff, 748px 977px #fff, 1637px 337px #fff, 851px 1514px #fff,
    1487px 1410px #fff, 1776px 710px #fff, 544px 453px #fff, 1707px 1932px #fff,
    1121px 1642px #fff, 1227px 391px #fff, 583px 833px #fff, 658px 278px #fff,
    345px 1388px #fff, 1529px 1419px #fff, 233px 1008px #fff, 892px 943px #fff,
    1431px 1091px #fff, 1524px 316px #fff, 1547px 192px #fff, 976px 36px #fff,
    1648px 1053px #fff, 1833px 1572px #fff, 1677px 936px #fff, 589px 1755px #fff,
    978px 1875px #fff, 1508px 412px #fff, 1242px 439px #fff, 1263px 40px #fff,
    1427px 1736px #fff, 639px 906px #fff, 1349px 373px #fff, 1055px 969px #fff,
    602px 95px #fff, 224px 1805px #fff, 1129px 837px #fff, 1110px 1358px #fff,
    1067px 1752px #fff, 391px 1389px #fff, 885px 1979px #fff, 1188px 414px #fff,
    1931px 325px #fff, 1853px 1918px #fff, 636px 1313px #fff, 1236px 1913px #fff,
    1801px 780px #fff, 633px 529px #fff, 1500px 33px #fff, 1387px 1045px #fff,
    832px 1281px #fff, 1880px 1845px #fff, 1477px 1096px #fff, 1457px 698px #fff,
    1658px 1049px #fff, 1957px 1151px #fff, 1561px 1593px #fff, 627px 250px #fff,
    975px 1575px #fff, 68px 998px #fff, 951px 85px #fff, 280px 431px #fff,
    1683px 1745px #fff, 322px 778px #fff, 841px 888px #fff, 1895px 1883px #fff,
    700px 568px #fff, 1846px 442px #fff, 91px 1650px #fff, 970px 917px #fff,
    1585px 452px #fff;
}

#stars3 {
  width: 3px;
  height: 3px;
  background: transparent;
  box-shadow: 1679px 1408px #fff, 1970px 1504px #fff, 1789px 965px #fff,
    698px 234px #fff, 1733px 1854px #fff, 1060px 271px #fff, 719px 1744px #fff,
    1707px 1847px #fff, 1710px 432px #fff, 1325px 1585px #fff, 92px 577px #fff,
    163px 1938px #fff, 1885px 123px #fff, 1566px 1753px #fff, 1288px 21px #fff,
    1396px 1908px #fff, 675px 1466px #fff, 734px 1557px #fff, 941px 1885px #fff,
    1692px 6px #fff, 115px 1183px #fff, 639px 1044px #fff, 1171px 1982px #fff,
    1801px 1078px #fff, 648px 820px #fff, 1885px 1984px #fff, 268px 1729px #fff,
    1388px 181px #fff, 1741px 1280px #fff, 1719px 1080px #fff, 12px 932px #fff,
    489px 157px #fff, 1910px 790px #fff, 115px 44px #fff, 1748px 1458px #fff,
    282px 109px #fff, 1100px 1528px #fff, 543px 598px #fff, 1320px 1188px #fff,
    1124px 839px #fff, 1406px 1289px #fff, 472px 1376px #fff, 852px 286px #fff,
    510px 860px #fff, 700px 306px #fff, 1822px 1302px #fff, 15px 19px #fff,
    1360px 420px #fff, 1483px 42px #fff, 1287px 1867px #fff, 1105px 1322px #fff,
    745px 161px #fff, 431px 1722px #fff, 855px 1254px #fff, 860px 1784px #fff,
    1578px 1955px #fff, 1085px 461px #fff, 472px 690px #fff, 23px 1152px #fff,
    1625px 601px #fff, 1177px 1692px #fff, 397px 1984px #fff, 10px 1164px #fff,
    1132px 1557px #fff, 438px 817px #fff, 1590px 1236px #fff, 1037px 1616px #fff,
    533px 941px #fff, 1163px 1992px #fff, 1451px 1081px #fff, 1335px 1578px #fff,
    503px 1556px #fff, 197px 1725px #fff, 511px 1397px #fff, 1514px 1164px #fff,
    1249px 148px #fff, 947px 1849px #fff, 1258px 1426px #fff, 255px 1937px #fff,
    23px 529px #fff, 578px 230px #fff, 925px 767px #fff, 903px 365px #fff,
    1861px 451px #fff, 1813px 912px #fff, 1597px 637px #fff, 195px 626px #fff,
    130px 404px #fff, 725px 408px #fff, 1916px 843px #fff, 1336px 239px #fff,
    1568px 1390px #fff, 139px 126px #fff, 1287px 1271px #fff, 731px 465px #fff,
    1959px 1465px #fff, 909px 169px #fff, 838px 1332px #fff, 464px 1037px #fff,
    1893px 233px #fff;
  /* animation: animStar 150s linear infinite; */
}

#stars3:after {
  content: ' ';
  position: absolute;
  /* top: 2000px; */
  width: 3px;
  height: 3px;
  background: transparent;
  box-shadow: 1679px 1408px #fff, 1970px 1504px #fff, 1789px 965px #fff,
    698px 234px #fff, 1733px 1854px #fff, 1060px 271px #fff, 719px 1744px #fff,
    1707px 1847px #fff, 1710px 432px #fff, 1325px 1585px #fff, 92px 577px #fff,
    163px 1938px #fff, 1885px 123px #fff, 1566px 1753px #fff, 1288px 21px #fff,
    1396px 1908px #fff, 675px 1466px #fff, 734px 1557px #fff, 941px 1885px #fff,
    1692px 6px #fff, 115px 1183px #fff, 639px 1044px #fff, 1171px 1982px #fff,
    1801px 1078px #fff, 648px 820px #fff, 1885px 1984px #fff, 268px 1729px #fff,
    1388px 181px #fff, 1741px 1280px #fff, 1719px 1080px #fff, 12px 932px #fff,
    489px 157px #fff, 1910px 790px #fff, 115px 44px #fff, 1748px 1458px #fff,
    282px 109px #fff, 1100px 1528px #fff, 543px 598px #fff, 1320px 1188px #fff,
    1124px 839px #fff, 1406px 1289px #fff, 472px 1376px #fff, 852px 286px #fff,
    510px 860px #fff, 700px 306px #fff, 1822px 1302px #fff, 15px 19px #fff,
    1360px 420px #fff, 1483px 42px #fff, 1287px 1867px #fff, 1105px 1322px #fff,
    745px 161px #fff, 431px 1722px #fff, 855px 1254px #fff, 860px 1784px #fff,
    1578px 1955px #fff, 1085px 461px #fff, 472px 690px #fff, 23px 1152px #fff,
    1625px 601px #fff, 1177px 1692px #fff, 397px 1984px #fff, 10px 1164px #fff,
    1132px 1557px #fff, 438px 817px #fff, 1590px 1236px #fff, 1037px 1616px #fff,
    533px 941px #fff, 1163px 1992px #fff, 1451px 1081px #fff, 1335px 1578px #fff,
    503px 1556px #fff, 197px 1725px #fff, 511px 1397px #fff, 1514px 1164px #fff,
    1249px 148px #fff, 947px 1849px #fff, 1258px 1426px #fff, 255px 1937px #fff,
    23px 529px #fff, 578px 230px #fff, 925px 767px #fff, 903px 365px #fff,
    1861px 451px #fff, 1813px 912px #fff, 1597px 637px #fff, 195px 626px #fff,
    130px 404px #fff, 725px 408px #fff, 1916px 843px #fff, 1336px 239px #fff,
    1568px 1390px #fff, 139px 126px #fff, 1287px 1271px #fff, 731px 465px #fff,
    1959px 1465px #fff, 909px 169px #fff, 838px 1332px #fff, 464px 1037px #fff,
    1893px 233px #fff;
}
</style>