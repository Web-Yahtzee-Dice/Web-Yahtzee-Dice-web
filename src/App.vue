<template>
  <button @click="roll()">roll</button>
  <div class="container_roll">
    <div v-for="(a,i) in dice" :key="i" class="container_dice" @click="save(i)" :style="{backgroundColor : st[i]}">{{a}}</div>
  </div>
  <div class="container_scoreboard">
    <div v-for="(_,i) in scoreboard_grid[0]" :key="i" class="area_scoreboard" :style="{gridArea : scoreboard_grid[0][i]}" style="color: black">{{scoreboard_rank[i]}}</div>
    <div v-for="(_,i) in scoreboard_grid[1]" :key="i" class="area_scoreboard" :style="{gridArea : scoreboard_grid[1][i]}" @click="fixScore(i)">{{score[0][i]}}</div>
    <div v-for="(_,i) in scoreboard_grid[2]" :key="i" class="area_scoreboard" :style="{gridArea : scoreboard_grid[2][i]}" >{{score[1][i]}}</div>
  </div>
</template>

<script>
export default {
  data(){
    return {
      scoreboard_grid : [['Aces','Deuces','Threes','Fours','Fives','Sixes','Subtotal', 'Bonus', 'Choice','4ofaKind','FullHouse','SmallStraight','LargeStraight','Yacht', 'Total'],
                        ['Aces_score1','Deuces_score1','Threes_score1','Fours_score1','Fives_score1','Sixes_score1','Subtotal_score1', 'Bonus_score1', 'Choice_score1','4ofaKind_score1','FullHouse_score1','SmallStraight_score1','LargeStraight_score1','Yacht_score1', 'Total_score1'],
                        ['Aces_score2','Deuces_score2','Threes_score2','Fours_score2','Fives_score2','Sixes_score2','Subtotal_score2', 'Bonus_score2', 'Choice_score2','4ofaKind_score2','FullHouse_score2','SmallStraight_score2','LargeStraight_score2','Yacht_score2', 'Total_score2']],
      scoreboard_rank : ['Aces','Deuces','Threes','Fours','Fives','Sixes','Subtotal', 'Bonus', 'Choice','4 of a Kind','Full House','Small Straight','Large Straight','Yacht', 'Total'],
      score : [[0,0,0,0,0,0,0,0,0,0,0,0,0,0,0],
              [0,0,0,0,0,0,0,0,0,0,0,0,0,0,0]],
      score_fix : [[false,false,false,false,false,false,false,false,false,false,false,false,false,false,false],
                  [false,false,false,false,false,false,false,false,false,false,false,false,false,false,false]],
      dice : [1,2,3,4,5],
      keep : [false,false,false,false,false],
      st : ['white', 'white', 'white', 'white', 'white']
    }
  },
  components: {
  },
  methods: {
    save(x){
      this.keep[x] = (this.keep[x] ? false : true);
      if (this.keep[x])
        this.st[x]='red';
      else
        this.st[x]='white';
    },
    roll(){
      let timecnt=0;
      const repeat=setInterval(() => {
          for (let i=0;i<5;i++){
            if (this.keep[i])
              continue;
            const ran=Math.floor(Math.random()*6)+1;
            this.dice[i]=ran;
          }
          timecnt++;
          if (timecnt>=10){
              clearInterval(repeat);
              this.scoring();
          }
      }, 50);
    },
    scoring(){
      let cnt=[0,0,0,0,0,0,0], sums=0;
      for (let i=0;i<5;i++){
        cnt[this.dice[i]]++;
        sums+=this.dice[i];
      }
      for (let i=0;i<this.score[1].length;i++){
        if (this.score_fix[0][i]===false)
          this.score[0][i]=0;
      }
      for (let i=0;i<6;i++){
        if (this.score_fix[0][i]===false)
          this.score[0][i]=cnt[i+1]*(i+1); // 1~6
      }
      this.score[0][8]=sums; // choice
      for (let i=1, tmp=0;i<=6;i++){
        if (cnt[i]==5) // yacht
          this.score[0][13]=50;
        if (cnt[i]>=4) // 4ofakind
          this.score[0][9]=sums;
        if (cnt[i]===5 || cnt[i]===3 || cnt[i]===2)
          tmp+=cnt[i];
        if (tmp===5) // fullhouse
          this.score[0][10]=sums;
      }
      for (let i=1;i<=3;i++){
        if (cnt[i]>0 && cnt[i+1]>0 && cnt[i+2]>0 && cnt[i+3]>0) // smallstraight
          this.score[0][11]=15;
        if (i<=2 && cnt[i]>0 && cnt[i+1]>0 && cnt[i+2]>0 && cnt[i+3]>0 && cnt[i+4]>0) // largestraight
          this.score[0][12]=30;
      }
    },
    fixScore(x){
      this.score_fix[0][x]=true;
    },

  },
}
</script>

<style>

  *{
  margin: 0;
  /* text drag x */
  -webkit-text-size-adjust: none;
  -ms-user-select: none; 
  -moz-user-select: -moz-none;
  -khtml-user-select: none;
  -webkit-user-select: none;
  user-select: none;
  }

  .container_scoreboard{
    display: grid;
    grid-template-rows: repeat(15, 40px);
    grid-template-columns: 160px 60px 60px;
    grid-template-areas:
      "Aces Aces_score1 Aces_score2"
      "Deuces Deuces_score1 Deuces_score2"
      "Threes Threes_score1 Threes_score2"
      "Fours Fours_score1 Fours_score2"
      "Fives Fives_score1 Fives_score2"
      "Sixes Sixes_score1 Sixes_score2"
      "Subtotal Subtotal_score1 Subtotal_score2"
      "Bonus Bonus_score1 Bonus_score2"
      "Choice Choice_score1 Choice_score2"
      "4ofaKind 4ofaKind_score1 4ofaKind_score2"
      "FullHouse FullHouse_score1 FullHouse_score2"
      "SmallStraight SmallStraight_score1 SmallStraight_score2"
      "LargeStraight LargeStraight_score1 LargeStraight_score2"
      "Yacht Yacht_score1 Yacht_score2"
      "Total Total_score1 Total_score2";
    line-height: 40px;
    vertical-align: middle;
    text-align: center;
  }

  .area_scoreboard{
    background-color: whitesmoke;
    border: 1px solid black;
    font-size: 20px;
    color: gray;
  }

  .container_roll{
    display: grid;
    grid-template-rows: 100px;
    grid-template-columns: repeat(5, 100px);
    grid-template-areas:
      "dice_1 dice_2 dice_3 dice_4 dice_5";
    vertical-align: middle;
    text-align: center;
  }

  .container_dice{
    width:35px;
    height:35px;
    line-height: 35px;
    vertical-align: middle;
    text-align: center;
    border: 3px solid black;
    border-radius:5px;
    margin: auto;
  }

  button{
    width:35px;
    height:35px;
    line-height: 35px;
    vertical-align: middle;
    text-align: center;
    border: 3px solid black;
    border-radius:5px;
    margin: auto;
    background-color: black;
    color: white;
  }
</style>
