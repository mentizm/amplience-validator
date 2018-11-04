<template>
  <div class="container-fluid">
    <div class="row">
      <div class="col-md-6">
        <button @click="parseUrl">URL prüfen</button>
        <br>
        <textarea v-model="data.input" placeholder="insert url"/></textarea>
        <br>
        <div class="content">
          <div>
            <span v-text="data.sub"></span>
          </div>
          <br>
        </div>
      </div>
      <div class="col-md-6">
        <input type="text" v-model="data.url.domain.correct" /><span v-text="data.url.domain.info"></span>
        <br>
        <input type="text" v-model="data.url.account.correct" /><span v-text="data.url.account.info"></span>
        <br>
        <input type="text" v-model="data.url.imageId.value" /><span v-text="data.url.parameters.info"></span>
        <br>
        <input type="text" v-model="data.url.parameters.concat" /><span v-text="data.url.parameters.info"></span>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "UrlParser",
  data() {
    return {
      data: {
        sub: '',
        input: 'https://i1.adis.ws/i/MDM/?$tpl_mf_sammlung_folderabo_barren_neu$&$ps_product_detail_slider_img$&muenze_1=1326370111_12&muenze_2=1326370111_22&muenze_3=1326360112_12&muenze_4=1326360112_22&muenze_5=1326350113_11&muenze_6=1326350113_22&muenze_7=1326380110_12&muenze_8=1326380110_22&muenze_9=1326390119_12&muenze_10=1326390119_22&muenze_11=1326400116_12&muenze_12=1326400116_22&muenze_13=1326410115_12&muenze_14=1326410115_22&wa_1=21653_Folder_Goldnoten_Euro&wa_2=21653_zf&wa_10=ps_21612_003',
        url:{
          domain: {
            correct: 'i1.adis.ws/i/',
            value: '',
            info: ''
          },
          account: {
            correct: 'MDM',
            value: '',
            info: ''
          },
          imageId: {
            value: '',
            info: ''
          },
          parameters: {
            list:{},
            concat: ''
          }
        },
        urlParsed: ''
      }
    }
  },
  methods: {
    parseUrl: function() {
      this.checkUrlDomain();
      this.urlParsed = this.data.url;
    },
    checkUrlDomain: function() {
      var input = this.data.input; 
      var urlDomain = this.data.url.domain.correct;  
      var regex = /(i1.adis.ws\/i\/)/;
      var match = regex.exec(input);
      console.log(match)

      if(match){
        this.data.sub = input.slice(input.lastIndexOf(urlDomain) + urlDomain.length);

        this.data.url.domain.info = 'Domain ist richtig';
        this.checkUrlAccount();
      } else {
        this.data.url.domain.info = 'Domain ist falsch';
      }

    },
    checkUrlAccount: function() {
      var input = this.data.input; 
      var urlAccount = this.data.url.account.correct;  
      var regex = /MDM/;
      var match = regex.exec(input);

      console.log(match)

      if(match){
        this.data.sub= input.slice(input.lastIndexOf(urlAccount) + urlAccount.length);
        this.data.url.account.info = 'Account ist richtig';
        this.checkImage();
      } else {
        this.data.url.account.info = 'Account ist falsch';
      }
    },
    checkImage: function(){
      var arr = this.data.sub.split('?');
      console.log(arr);
      var image_arr = arr[0].split('/');
      console.log(image_arr);
      if(image_arr.length > 1){
        this.data.url.imageId.value = image_arr[1];
        this.checkImageExist()
      }
    },
    checkImageExist: function(){
      this.httpGetAsync('https://i1.adis.ws/i/MDM/20652_mueller_neu?', function(a,b){ 
        console.log(a);
      })
    },
    httpGetAsync(theUrl, callback) {
      var xmlHttp = new XMLHttpRequest();
      xmlHttp.onreadystatechange = function () {
          callback({
            status: xmlHttp.status,
            state: xmlHttp.readyState
            });
      }
      xmlHttp.open("GET", theUrl, true); // true for asynchronous 
      xmlHttp.send(null);
    }
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.main{
}
.content{
  word-wrap: break-word;
  text-align: left;
  border: 1px solid #000;
  padding: 10px;
}
textarea{
  width: 100%;
  height: 24em ;
}
.content input{
}
</style>
