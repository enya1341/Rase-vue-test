<template>
  <div id="store">
    <div class="box">

      <img :src="Storeimage(this.parentStore.image)">
      <!-- <img :src="this.image"> -->
      <div class="bottom-box">
        <h1>{{this.parentStore.name}}</h1>
        <h3>#{{this.parentStore.region}} #{{this.parentStore.genre}}</h3>
        <div class = "value">
          <p>評価:{{this.values}}</p>
          <p>{{this.count}}件</p>
        </div>
        <div class="flex">
          <button @click="StoreDetail">詳しくみる</button>
          <img v-if="this.favorite===true" src='../assets/heartin.png' @click="Favoritedata">
          <img v-else src='../assets/heart.png' @click="Favoritedata">
        </div>
      </div>

    </div>
  </div>
</template>

<script>



import axios from "axios";



export  default{


  props: ["parentFavorite","parentStore"],


  data(){
    return{
      reservation_id:"",
      favorite_id:"",
      valuedata:[],
      values:0,
      count:0,
      favorite:false,
      image:require(`./../assets/store/7.jpg`)
    }
  },
  

  mounted: function(){
    this.FavoriteGet();
    this.ValueGet();
    console.log("mountedが実行されました")
  },


  methods:{
    
    /** ストア詳細ページに遷移*/
    StoreDetail(){
      this.$router.push({
        name: 'StoreDetail',
        params: { id: this.parentStore.id }
      })
    },

    /**お気に入りデータの追加と削除。一気に一つに纏めた*/
    Favoritedata(){
      if(this.favorite===false){
        this.favorite=true;

        axios.post(this.$store.state.host + "/api/v1/" + this.$store.state.user.id + "/favorites",{
          store_id:this.parentStore.id
        }).then((response) => {this.reservation_id =response.data.data.id});

      }else{
        this.favorite=false;

        axios({
              method: "delete",
              url: this.$store.state.host + "/api/v1/"+this.$store.state.user.id+"/favorites",
              data: {
                store_id:this.parentStore.id
              }
        })

      }
    },

    async ValueGet(){
      await axios.get(this.$store.state.host + "/api/v1/" + this.parentStore.id + "/values").then((response) => {this.valuedata =response.data.data})

      for(let i = 0;i < this.valuedata.length;i++){
        this.values = this.values + this.valuedata[i].value;
        this.count += 1;
      }
      
      if(this.valuedata.length){
        this.values = this.values / this.count;
      }
      
    },


    /** 動的にrequireを機能させるための関数*/
    Storeimage(imgURL){
      return require(`../../public/store/${imgURL}`)
    },

    /** お気に入りデータが入っている時の処理。これで可否を行う*/
    FavoriteGet(){
      for(let Favoritedata of this.parentFavorite){
        if(Favoritedata.store_id === this.parentStore.id){
          this.favorite=true;
          break;
        }else{
          this.favorite=false;
        }
      }
    
    },

  },


}
</script>

<style scoped>

.box {
  width:100%;
  height:280px;
  background-color: white;
  border-radius: 10px;
  
}

.box img {
  width:100%;
  height:50%;

}

.bottom-box{
  background-color: white;
  height:50%;
  padding:10px 15px;
  color:black;
}

.bottom-box h1{
  font-weight: bold;
  margin-bottom:15px;
  color:black;
}

.bottom-box h3{
  margin-bottom:15px;
  color:black;
}

.value{
  display: flex;
}

.value p{
  color:black;
  margin-right:30px;
}

.bottom-box img{
  width:30px;
  margin:15px;
}

button {
  width: 100px;
  height:45px;
  text-align: center;
  padding: 8px 0 10px;
  color: #fff;
  background-color: #2f00ff;
  border-radius: 10px;
  cursor: pointer;
  margin-top:10px;
}

.flex {
  display: flex;
  justify-content: space-between;
}


</style>