<template>
  <button @click="roll()">roll</button>
  <div class="container_roll">
    <div v-for="(a,i) in dice" :key="i" class="container_dice" @click="save(i)" :style="{backgroundColor : st[i]}">{{a}}</div>
  </div>
  <div class="container_scoreboard">
    <div v-for="(a,i) in scoreboard_grid[0]" :key="i" class="area_scoreboard" :style="{gridArea : scoreboard_grid[0][i]}">
      {{score[0][i]}}
    </div>
    <div v-for="(a,i) in scoreboard_grid[1]" :key="i" class="area_scoreboard" :style="{gridArea : scoreboard_grid[1][i]}">
      {{score[1][i]}}
    </div>
    
  </div>
</template>

<script>
export default {
  data(){
    return {
      scoreboard_grid : [['Aces','Deuces','Threes','Fours','Fives','Sixes','Subtotal', 'Bonus', 'Choice','4ofaKind','FullHouse','SmallStraight','LargeStraight','Yacht', 'Total'],['Aces_score','Deuces_score','Threes_score','Fours_score','Fives_score','Sixes_score','Subtotal_score', 'Bonus_score', 'Choice_score','4ofaKind_score','FullHouse_score','SmallStraight_score','LargeStraight_score','Yacht_score', 'Total_score']],
      score : [['Aces','Deuces','Threes','Fours','Fives','Sixes','Subtotal', 'Bonus', 'Choice','4 of a Kind','Full House','Small Straight','Large Straight','Yacht', 'Total'],[0,0,0,0,0,0,0,0,0,0,0,0,0,0,0]],
      score_fix : [0,0,0,0,0,0,0,0,0,0,0,0,0,0,0],
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
      for (let i=0;i<this.score[1].length;i++)
        this.score[1][i]=0;
      for (let i=0;i<6;i++){
        this.score[1][i]=cnt[i+1]*(i+1); // 1~6
      }
      this.score[1][6]=sums;
      for (let i=1, tmp=0;i<=6;i++){
        if (cnt[i]==5) // yacht
          this.score[1][13]=50;
        if (cnt[i]>=4) // 4ofakind
          this.score[1][9]=sums;
        if (cnt[i]===5 || cnt[i]===3 || cnt[i]===2)
          tmp+=cnt[i];
        if (tmp===5) // fullhouse
          this.score[1][10]=sums;
      }
      for (let i=1;i<=3;i++){
        if (cnt[i]>0 && cnt[i+1]>0 && cnt[i+2]>0 && cnt[i+3]>0) // smallstraight
          this.score[1][9]=15;
        if (i<=2 && cnt[i]>0 && cnt[i+1]>0 && cnt[i+2]>0 && cnt[i+3]>0 && cnt[i+4]>0) // largestraight
          this.score[1][10]=30;
      }
    }
  },
}
</script>

<style>

  .container_scoreboard{
    display: grid;
    grid-template-rows: repeat(15, 40px);
    grid-template-columns: 150px 100px;
    grid-template-areas:
      "Aces Aces_score"
      "Deuces Deuces_score"
      "Threes Threes_score"
      "Fours Fours_score"
      "Fives Fives_score"
      "Sixes Sixes_score"
      "Subtotal Subtotal_score"
      "Bonus Bonus_score"
      "Choice Choice_score"
      "4ofaKind 4ofaKind_score"
      "FullHouse FullHouse_score"
      "SmallStraight SmallStraight_score"
      "LargeStraight LargeStraight_score"
      "Yacht Yacht_score"
      "Total Total_score";
    line-height: 40px;
    vertical-align: middle;
    text-align: center;
  }

  .area_scoreboard{
    background-color: bisque;
    border: 1px solid black;
    font-size: 20px;
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
