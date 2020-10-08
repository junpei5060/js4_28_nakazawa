<template id="main">
  <div id="app">
    <h1 class="title">Measure your face age</h1>
    <canvas ref="canvas" id="emo_canvas" width="300" height="240"></canvas>
    <p>Your face age is...{{ emotion }}  years old</p>
    <div>
      <p class="nowface">Now face</p>
      <video ref="video" id="video" width="300" height="240"></video>
    
    </div>

  </div>
</template>


<script>
import Vue from 'vue'
import Axios from 'axios'
import VueAxios from 'vue-axios'

Vue.use(VueAxios, Axios) 

export default ({
  template: '#main',
  data() {
    return {
      video: {},
      canvas: {},
      captures: [],
      testTimer: '',
      emotion:'',
    };
  },
  mounted() {
  //カメラを起動し、映像をvideoタグ内に表示
    this.video = this.$refs.video
    if (navigator.mediaDevices && navigator.mediaDevices.getUserMedia) {
      navigator.mediaDevices.getUserMedia({audio: false, video: true})
      .then(stream => {
        this.video.srcObject = stream
        this.video.play()
      })
    }

    console.log(this.$refs.canvas)
    // console.log(this.$refs.canvas)

       /* eslint-disable */
    this.canvas = this.$refs.canvas 
    this.testTimer = setInterval(() => {
      // console.log(this.$refs.canvas)
      let context = this.canvas.getContext("2d").drawImage(this.video, 0, 0, 400, 240)
      this.captures.push(this.canvas.toDataURL("image/png")) //撮った画像をcaptures配列に格納する
      let subscriptionKey = "951ca53d72ea41ac82f9697c50456994";
      let uriBase = "https://westcentralus.api.cognitive.microsoft.com/face/v1.0/detect";
      let params = {
        "returnFaceId": "true",
        "returnFaceLandmarks": "false",
        "returnFaceAttributes":
           "age,gender,headPose,smile,facialHair,glasses,emotion," +
            "hair,makeup,occlusion,accessories,blur,exposure,noise"
      };
      /* eslint-disable */
      //配列最後に追加された画像のフォーマットを変換し、imgURL変数に代入する
      const imgURL = this.makeblob(this.captures[this.captures.length - 1])
      //imgURLの画像をFaceAPIに送信
      Axios.post(
        uriBase + "?returnFaceId=true&returnFaceLandmarks=false&returnFaceAttributes=age,emotion",
        imgURL,
        {
          headers: {
            "Content-Type": "application/octet-stream",
            "Ocp-Apim-Subscription-Key": subscriptionKey,
          }
        },
      )
      .then(response => {
        this.emotion = response.data[0].faceAttributes.age
        console.log(response.data[0].faceAttributes.age)

      })
      .catch(error => {
        // console.log(error.response)
      });
    }, 5000);
  },
  methods: {     
    makeblob: function (dataURL) {
      let BASE64_MARKER = ';base64,';
      if (dataURL.indexOf(BASE64_MARKER) == -1) {
        let parts = dataURL.split(',');
        let contentType = parts[0].split(':')[1];
        let raw = decodeURIComponent(parts[1]);
        return new Blob([raw], {type: contentType});
      }
      let parts = dataURL.split(BASE64_MARKER);
      let contentType = parts[0].split(':')[1];
      let raw = window.atob(parts[1]);
      let rawLength = raw.length;
      let uInt8Array = new Uint8Array(rawLength);
      for (let i = 0; i < rawLength; ++i) {
        uInt8Array[i] = raw.charCodeAt(i);
      }
      return new Blob([uInt8Array], {type: contentType})
    }
  }
});

</script>

<style scoped>
#main{
  margin: 0;
}

#app{
  margin: 0;
}
.title{
  margin: 0;
}

#emo_canvas{
  padding: 0.5em 1em;
  margin: 2em 0;
  border: double 5px #4ec4d3;
}
#video{
  padding: 0.5em 1em;
  margin: 2em 0;
  border: double 5px #4ec4d3;
}

.registeredface{
  color: blue;
  margin: 0;
}

.nowface{
  color: red;
  margin: 0;
}

/* 背景
@import url('https://fonts.googleapis.com/css?family=Exo:400,700');

*{
    margin: 0px;
    padding: 0px;
}

body{
    font-family: 'Exo', sans-serif;
}


.context {
    width: 100%;
    position: absolute;
    top:50vh;
    
}

.context h1{
    text-align: center;
    color: #fff;
    font-size: 50px;
}


.area{
    background: #4e54c8;  
    background: -webkit-linear-gradient(to left, #8f94fb, #4e54c8);  
    width: 100%;
    height:100vh;
    
   
}

.circles{
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    overflow: hidden;
}

.circles li{
    position: absolute;
    display: block;
    list-style: none;
    width: 20px;
    height: 20px;
    background: rgba(255, 255, 255, 0.2);
    animation: animate 25s linear infinite;
    bottom: -150px;
    
}

.circles li:nth-child(1){
    left: 25%;
    width: 80px;
    height: 80px;
    animation-delay: 0s;
}


.circles li:nth-child(2){
    left: 10%;
    width: 20px;
    height: 20px;
    animation-delay: 2s;
    animation-duration: 12s;
}

.circles li:nth-child(3){
    left: 70%;
    width: 20px;
    height: 20px;
    animation-delay: 4s;
}

.circles li:nth-child(4){
    left: 40%;
    width: 60px;
    height: 60px;
    animation-delay: 0s;
    animation-duration: 18s;
}

.circles li:nth-child(5){
    left: 65%;
    width: 20px;
    height: 20px;
    animation-delay: 0s;
}

.circles li:nth-child(6){
    left: 75%;
    width: 110px;
    height: 110px;
    animation-delay: 3s;
}

.circles li:nth-child(7){
    left: 35%;
    width: 150px;
    height: 150px;
    animation-delay: 7s;
}

.circles li:nth-child(8){
    left: 50%;
    width: 25px;
    height: 25px;
    animation-delay: 15s;
    animation-duration: 45s;
}

.circles li:nth-child(9){
    left: 20%;
    width: 15px;
    height: 15px;
    animation-delay: 2s;
    animation-duration: 35s;
}

.circles li:nth-child(10){
    left: 85%;
    width: 150px;
    height: 150px;
    animation-delay: 0s;
    animation-duration: 11s;
}



@keyframes animate {

    0%{
        transform: translateY(0) rotate(0deg);
        opacity: 1;
        border-radius: 0;
    }

    100%{
        transform: translateY(-1000px) rotate(720deg);
        opacity: 0;
        border-radius: 50%;
    }

} */
</style>