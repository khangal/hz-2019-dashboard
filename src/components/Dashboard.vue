<template>
<div>
<header>
      <div class="main-object center">
        <img id="logo" src="@/assets/logo-main.png">
        <h3>STARWARS <br class="br">VERSION</h3>
      </div >
      <div class="header-flex">
        <p>HARUUL ZANGi CTF 2019</p>
        <p>FINAL ROUND</p>
      </div>
    </header>
  <div class="container">
    
    <div class="main" style="z-index:1000">
      <!-- <div class="start-line">
        <img src="@/assets/start.png">
      </div> -->
      <div
        :ref="`${team.id}-shipOuter`"
        v-for="team in teams"
        :key="`ship-${team.name}`"
      >
        <div :ref="`${team.id}-shipContainer`" class="ship-container">
          <span :ref="`${team.id}-ship`" :class="`team--` + team.id" class="team-icon"  > </span>
        </div>
      </div>
    </div>
    <transition-group
      name="team-list"
      tag="ul"
      class="scoreboard"
      style="z-index:10000"
    >
      <li
        v-for="team in sortedTeams"
        :key="team.name"
        class="score-item"
      >
      <div>
        <span class="head"><img :class="`team` + team.id" :src="require(`@/assets/heads/`+team.id + `.png`)"></span>
        <span class="team-name">{{ team.name }}</span>
      </div>
      <div>
        <span class="score">
          {{ `${team.score}`.padStart(2, "0") }}
        </span>
      </div>
      </li>
    </transition-group>
  </div>
  </div>
</template>

<script>
import anime from "animejs";
import moment from "moment";
import axios from "axios";
import "@/assets/Font/starwars.css"
import { BASE_URL } from "@/constants"

// const duration = 10 * 3600 * 5
const duration = 1000 * 3600 * 5;
const startTime = moment("2019-10-02 12:10");

// 15000 100%
// 5433 30%

const TIME_PERCENT = 80;
const POINT_PERCENT = 20;

export default {
  name: "Dashboard",
  props: {
    msg: String
  },
  data() {
    return {
      teams: []
    };
  },
  mounted() {
    const promise1 = axios.get(`${BASE_URL}/api/v1/teams`, {}).then((res) => {
      this.teams = res.data.data.map(team => {
        return {
          id: team.id,
          name: team.name,
          score: 0
        }
      })
    })

    const promise2 = axios.get(`${BASE_URL}/api/v1/scoreboard`, {})
      .then((res) => {
        res.data.data.forEach((teamWithScore) => {
          let team = this.teams.find(team => team.name === teamWithScore.name)
          team.score = teamWithScore.score
        })
      })

    Promise.all([promise1, promise2]).then(() => {
      this.go()

      setInterval(() => {
        axios.get(`${BASE_URL}/api/v1/scoreboard`, {})
          .then((res) => {
            res.data.data.forEach((teamWithScore) => {
              let team = this.teams.find(team => team.name === teamWithScore.name)
              if (team.score !== teamWithScore.score) {

                team.score = teamWithScore.score
                this.boost(team.id, team.score)
              }
            })
          })
      }, 1000);
    })
  },
  computed: {
    sortedTeams() {
      return this.teams.concat().sort((a, b) => b.score - a.score);
    }
  },
  methods: {
    go() {
      let progress = moment.duration(moment().diff(startTime)).asMilliseconds();
      let seekProgress = (progress * 100) / duration;
      seekProgress = 98

      if (seekProgress > 100) {
        seekProgress = 99.99999
      }

      this.teams.forEach(team => {
        let timeAnimation = anime({
          targets: this.$refs[`${team.id}-shipContainer`],
          translateX: `${TIME_PERCENT}%`,
          duration: duration,
          easing: "linear"
        });

        timeAnimation.pause();
        timeAnimation.seek((duration * seekProgress) / 100);
        timeAnimation.play();
      });

      this.movePoint(0);
    },
    boost(teamId, score) {
      const team = this.teams.find(team => team.id === teamId);
      team.score = score;

      this.movePoint(3000);
    },

    movePoint(pointDuration) {
      const maxScore = Math.max(...this.teams.map(team => team.score));

      this.teams.forEach(team => {
        anime({
          targets: this.$refs[`${team.id}-shipOuter`],
          translateX: `${((team.score * 100) / maxScore) *
            (POINT_PERCENT / 100)}%`,
          duration: pointDuration,
          easing: "linear"
        });
      });
    }
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style lang="scss" scoped>
.container {
  display: flex;
  z-index: 100;
}

.ship-container {
  // margin-bottom: 1vh;
}

.team-list-move {
  /* applied to the element when moving */
  transition: transform 0.5s cubic-bezier(0.55, 0, 0.1, 1);
  transition: transform 1s;
}

h3 {
  margin: 40px 0 0;
}
header{
  margin: 0 3%;
  height: 20vh;
  #logo{
    width: 7%;
  }
  .br{
    margin-top: 5px;
  }
  .main-object{
    position: absolute;
    text-align: center;
  }
  .header-flex{
    display: flex;
    justify-content: space-between;
    p{
      font-size: 2vw;
    }
  }
}
.main {
  display: flex;
  flex-direction: column;
  justify-content: flex-end;
  padding-left: 1vw;
  padding-right: 1vw;
  padding-top: 2vh;
  padding-bottom: 2vh;
  text-align: left;
  width: 75vw;
  height: 80vh;
}

.scoreboard {
  font-size: 32px;
  margin-left: auto;
  width: 20vw;
  padding: 0;
  padding-left: 2vw;
  list-style-type: none;
  margin-right: 2vw;
}

.score-item {
  display: flex;
  justify-content: space-between;
  text-align: left;
  margin: 1.5vw 0;
  color: white;
}
.score-item:first-child{
      margin: 0;
}
.team-icon {
  display: inline-block;
  width: 7vh;
  height: 7vh;
  background-size: contain;
  background-position: center;
  background-repeat: no-repeat
}

.team--1 {
  background-image: url("~@/assets/Characters/1.png");
}
.team--2 {
  background-image: url("~@/assets/Characters/2.png");
}
.team--3 {
  background-image: url("~@/assets/Characters/3.png");
}
.team--4 {
  background-image: url("~@/assets/Characters/4.png");
}
.team--5 {
  background-image: url("~@/assets/Characters/5.png");
}
.team--6 {
  background-image: url("~@/assets/Characters/6.png");
}
.team--7 {
  background-image: url("~@/assets/Characters/7.png");
}
.team--8 {
  background-image: url("~@/assets/Characters/8.png");
}
.team--9 {
  background-image: url("~@/assets/Characters/9.png");
}
.team--10 {
  background-image: url("~@/assets/Characters/10.png");
}
.head{
  // .team1{
  //   width: 3vw;
  //   height: 1.7vw;
  // }
  // .team2{
  //   height: 2.5vw;
  // }
  // .team3{
  //   height: 2vw;
  // }
  // .team4{
  //   height: 2.6vw;
  // }
  // .team5{
  //   height: 2.4vw;
  // }
  // .team6{
  //   height: 2.7vw;
  // }
  // .team7{
  //   height: 2.1vw;
  //   width: 2.5vw;
  // }
  img{
    width: 12%;
    height: 55%;
    margin-right: 1vw;
  }
}
.start-line{
  position: absolute;
}
</style>
