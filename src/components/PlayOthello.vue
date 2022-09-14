<template>
  <div class="hello" style="padding:2% 0%;">
    <div class="centerMiddle">
      <table class="inner" style="margin-bottom:10px">
        <tr v-for="y in size" :key="y">
          <td v-for="x in size" @click="onStoneSet(x, y)" :key="x">
            <stone :type="getStone(x, y)"></stone>
          </td>
        </tr>
      </table>

    </div>
    
    <span style="background:#d8bfd8;padding:20px 20px; margin:50px;border-radius:8px;">
      <stone :type="currentStoneId"></stone> の番
    </span>
    <button id="skip" @click="this.currentStoneId = this.currentStoneId * -1">スキップ</button>
    <button id="reset" @click="reset()">リセット</button>
  </div>
</template>

<script>
import stoneComponent from "./stoneComponent.vue"

export default {
  name: 'HelloWorld',
  props: {
    msg: String
  },
  components: {
    stone: stoneComponent,
  },
  data(){
    return{
      size: 14,
      stones: [],
      currentStoneId: -1,    // -1 => 黒い石、1 => 白い石,
      result:0,
      init:0,
    }
  },
  methods:{
    initStone() {
      var stones = [];
      var size = this.size;
      this.init = 1;
      for(var x = 0 ; x < size ; x++) {
        var stoneLine = [];
        for(var y = 0 ; y < size ; y++) {
          if( (x==6 && y==6) || (x==7 && y==7) ){
            stoneLine.push(-1);  
          }else if( (x==7 && y==6) || (x==6 && y==7) ){
            stoneLine.push(1);
          }else{
            stoneLine.push(0);
          }
        }
        stones.push(stoneLine);
      }
      this.stones = stones;
    },

    getStone(x, y) {
      return this.stones[x-1][y-1];        
    },

    reset() {
      var stones = [];
      var size = this.size;
      this.init = 1;
      for(var x = 0 ; x < size ; x++) {
        var stoneLine = [];
        for(var y = 0 ; y < size ; y++) {
          if( (x==6 && y==6) || (x==7 && y==7) ){
            stoneLine.push(-1);  
          }else if( (x==7 && y==6) || (x==6 && y==7) ){
            stoneLine.push(1);
          }else{
            stoneLine.push(0);
          }
        }
        stones.push(stoneLine);
      }
      this.stones = stones;
    },

    copyStone() {     
//      return JSON.parse(JSON.stringify(this.stones));
      return this.stones;
    },

    changeStone(x, y) { // 同じ色で挟まれた場合にひっくり返す
      var movingCollection = [    // 移動する方向
        {x: -1, y: -1}, // 左上
        {x:  0, y: -1}, // 上
        {x:  1, y: -1}, // 右上
        {x:  1, y:  0}, // 右
        {x:  1, y:  1}, // 右下
        {x:  0, y:  1}, // 下
        {x: -1, y:  1}, // 左下
        {x: -1, y:  0}, // 左
      ];
      var baseX = x - 1;  // 置かれた石の座標：X
      var baseY = y - 1;  // 置かれた石の座標：Y
      var changingStoneId = this.currentStoneId * -1;    // ひっくり返す石

      for(var i = 0 ; i < movingCollection.length ; i++) {    //movingCollection.length:8
        var moving = movingCollection[i];
        var checkingX = baseX;
        var checkingY = baseY;
        var changingPositions = [];     // ひっくり返す（かもしれない）座標

        innerLoop:
        for(var j = 0 ; j < this.size ; j++) {  //置いた場所の周囲をチェック
          checkingX += moving.x;   // チェックする場所を移動
          checkingY += moving.y;   // チェックする場所を移動
          if( checkingX < 0 ||
              checkingY < 0 ||
              checkingX >= this.size ||
              checkingY >= this.size) {   // 盤上を出た
          break innerLoop;
          }
          var checkingStoneId = this.stones[checkingX][checkingY];
          if(checkingStoneId == this.currentStoneId) {   // 自分の色だった場合
            var newStones = this.copyStone();
            for(var k = 0 ; k < changingPositions.length ; k++) {
              var changingPosition = changingPositions[k];
              var changingX = changingPosition.x;
              var changingY = changingPosition.y;
              newStones[changingX][changingY] = this.currentStoneId;
            }
            this.stones = newStones;
            break innerLoop;
          }else if(checkingStoneId == changingStoneId) {   // 相手の色だった場合
            changingPositions.push({x: checkingX, y: checkingY});
          }else{    // 空白の場合
            break innerLoop;
          }
        }
      }
    },

    checkStone(x, y) { // 同じ色で挟まれた場合にひっくり返す
      var movingCollection = [    // 移動する方向
        {x: -1, y: -1}, // 左上
        {x:  0, y: -1}, // 上
        {x:  1, y: -1}, // 右上
        {x:  1, y:  0}, // 右
        {x:  1, y:  1}, // 右下
        {x:  0, y:  1}, // 下
        {x: -1, y:  1}, // 左下
        {x: -1, y:  0}, // 左
      ];
      var baseX = x - 1;  // 置かれた石の座標：X
      var baseY = y - 1;  // 置かれた石の座標：Y
      var changingStoneId = this.currentStoneId * -1;    // ひっくり返す石
      var sameColor = 0;
      var differentColor = 0;

      for(var i = 0 ; i < movingCollection.length ; i++) {    //movingCollection.length:8
        var moving = movingCollection[i];
        var checkingX = baseX;
        var checkingY = baseY;

        innerLoop:
        for(var j = 0 ; j < this.size ; j++) {  //置いた場所の周囲をチェック
          checkingX += moving.x;   // チェックする場所を移動
          checkingY += moving.y;   // チェックする場所を移動
          if( checkingX < 0 ||
              checkingY < 0 ||
              checkingX >= this.size ||
              checkingY >= this.size) {   // 盤上を出た
              if(sameColor != 1){
                differentColor = 0;
              }
          break innerLoop;
          }

          var checkingStoneId = this.stones[checkingX][checkingY];
          if(checkingStoneId == this.currentStoneId) {   // 自分の色だった場合
            if(differentColor == 1){
              sameColor = 1;
            }else{
              differentColor = 0;  
            }
            break innerLoop;
          }else if(checkingStoneId == changingStoneId) {   // 相手の色だった場合
            differentColor = 1;
          }
          else{    // 空白の場合
            if( (differentColor!=1) || (sameColor!=1) ){
              differentColor = 0;
              sameColor = 0;
            }
            break innerLoop;
          }
        }
      }
      if( (sameColor == 1)  &&  (differentColor == 1) ){
        this.result = 1;
      }else{
        this.result = 0;
      }
      return this.result;
    },

    stoneCounts() {
      var counts = {
          black: 0,
          white: 0,
          total: 0
      };
      var size = this.size;
      for(var x = 0 ; x < size ; x++) {
        for(var y = 0 ; y < size ; y++) {
          var stoneId = this.stones[x][y];
          if(stoneId == -1 || stoneId == 1) {
            if(stoneId == -1) {
              counts.black++;
            } else if(stoneId == 1) {
              counts.white++;
            }
            counts.total++;
          }
        }
      }
      return counts;
    },

    onStoneSet(x, y) {
      this.init = 0;
      var chechResult = this.checkStone(x, y);
      if(chechResult == 1){
        if(this.stones[x-1][y-1] == 0) {    // 選んだ場所が空白かどうか？
          var newStones = this.copyStone();
          newStones[x-1][y-1] = this.currentStoneId;
          this.stones = newStones;      
            this.changeStone(x, y);
          this.currentStoneId *= -1;  // 白黒交代
          var fullCount = this.size * this.size;
          var stoneCounts = this.stoneCounts();
          if(stoneCounts.total == fullCount) {
            if(stoneCounts.black > stoneCounts.white) {
              alert("黒の勝ち");
            } else if(stoneCounts.black < stoneCounts.white) {
              alert("白の勝ち");
            } else {
              alert("引き分け");
            }
          }
//        } else {
//          alert('すでに石が置かれています');
        }
      }
    },
  },

  created:function() {
      this.initStone();
  },
}//methods
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
  table {
    border-collapse: collapse;
  }

  td {
    border: 3px solid black;
    background: #009E54;
    width: 50px;
    height: 50px;
    text-align: center;
    vertical-align: middle;
  }

  .hello{
    width:800px; height:200px; margin:auto; margin-top: 0%;
  }

  #skip{
    background:blue;
    color:white;
    padding:20px 20px;
    border-radius:8px;
  }  

  #reset{
    background:greenyellow;
    color:black;
    padding:20px 20px;
    border-radius:8px;
    margin-left:50px
  }  

</style>
