<template>
  <div>
    <header>
      <div class="main-object center">
        <img id="logo" src="@/assets/logo-main.png" />
        <h3>STARWARS <br class="br" />VERSION</h3>
      </div>
      <div class="header-flex">
        <audio id="myAudio" style="display:none">
          <source :src="require(`@/assets/new-score.mp3`)" type="audio/mpeg" />
          Your browser does not support the audio element.
        </audio>
        <p>HARUUL ZANGi CTF 2019</p>
        <p>FINAL ROUND</p>
      </div>
    </header>
    <div class="container">
      <!-- <img src="@/assets/death-star.png" alt="" style="height:10vh;position:absolute;left:30%; top:5%; mask-image: linear-gradient(180deg, rgba(0,0,0,1), rgba(0,0,0,0)"> -->
      <div class="main" style="z-index:1000">
        <div class="start-line">
          <span class="start-text">Start Line</span>
          <img src="@/assets/start.svg" />
        </div>
        <div
          :ref="`${team.id}-shipOuter`"
          v-for="team in teams"
          :key="`ship-${team.name}`"
          class="ships"
        >
          <div
            :ref="`${team.id}-shipContainer`"
            class="ship-container"
            style="position: relative"
          >
            <img
              :ref="`team${team.id}-lightning`"
              :class="`team${team.id}-lightning`"
              :src="require('@/assets/lightning1.gif')"
              style="position:absolute;width:100px;height:auto;left:-15px;opacity:0"
            />
            <img
              :ref="`team${team.id}-lightning2`"
              :class="`team${team.id}-lightning2`"
              :src="require('@/assets/lightning2.gif')"
              style="position:absolute;width:100px;height:auto;left:-80px;top:-10px;opacity:0"
            />
            <span
              :ref="`${team.id}-ship`"
              :class="`team--` + team.id"
              class="team-icon"
            >
            </span>
          </div>
        </div>
        <div class="finish-line">
          <img src="@/assets/finish.svg" />
          <span class="finish-text">Finish Line</span>
        </div>
      </div>

      <transition-group
        name="team-list"
        tag="ul"
        class="scoreboard"
        style="z-index:10000"
      >
        <li v-for="team in sortedTeams" :key="team.name" class="score-item">
          <div>
            <span class="team-pos">{{ team.pos }} .</span>
            <span class="head"
              ><img
                :class="`team` + team.id"
                :src="require(`@/assets/heads/` + team.id + `.png`)"
            /></span>
            <span class="team-name" :class="positionClass(team.pos)">{{
              team.name
            }}</span>
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
import "@/assets/Font/starwars.css";
import { BASE_URL } from "@/constants";

// const duration = 10 * 3600 * 5
const duration = 1000 * 3600 * 6;
const startTime = moment("2019-10-04 12:00");

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
    const promise1 = axios.get(`${BASE_URL}/api/v1/teams`, {}).then(res => {
      this.teams = res.data.data.map(team => {
        return {
          id: team.id,
          name: team.name,
          score: 0,
          pos: 10
        };
      });
    });

    const promise2 = axios
      .get(`${BASE_URL}/api/v1/scoreboard`, {})
      .then(res => {
        res.data.data.forEach(teamWithScore => {
          let team = this.teams.find(team => team.name === teamWithScore.name);
          team.score = teamWithScore.score;
          team.pos = teamWithScore.pos;
        });
      });

    Promise.all([promise1, promise2]).then(() => {
      this.go();

      setInterval(() => {
        axios.get(`${BASE_URL}/api/v1/scoreboard`, {}).then(res => {
          res.data.data.forEach(teamWithScore => {
            let team = this.teams.find(
              team => team.name === teamWithScore.name
            );

            if (team.score !== teamWithScore.score) {

              if (teamWithScore.score > team.score) {
                if (this.$refs[`team${team.id}-lightning`][0]) {
                  this.$refs[`team${team.id}-lightning`][0].style.opacity = 100;
                }
                
                if (this.$refs[`team${team.id}-lightning2`][0]) {
                  this.$refs[`team${team.id}-lightning2`][0].style.opacity = 100;
                }

                this.playNewScore();
              }

              this.boost(teamWithScore);
            }
          });
        });
      }, 1000);
    });
  },

  computed: {
    sortedTeams() {
      return this.teams.concat().sort((a, b) => a.pos - b.pos);
    }
  },
  methods: {
    go() {
      let progress = moment.duration(moment().diff(startTime)).asMilliseconds();
      let seekProgress = (progress * 100) / duration;

      if (seekProgress > 100) {
        seekProgress = 99.99999;
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
    positionClass(position) {
      if (position === 1) {
        return "gold";
      } else if (position === 2) {
        return "silver";
      } else if (position === 3) {
        return "bronze";
      }
      return "";
    },
    boost(teamWithScore) {
      const team = this.teams.find(team => team.name === teamWithScore.name);
      team.score = teamWithScore.score;
      team.pos = teamWithScore.pos;

      this.movePoint(
        3000,
        this.$refs[`team${team.id}-lightning`][0],
        this.$refs[`team${team.id}-lightning2`][0]
      );
    },

    playNewScore() {
      var x = document.getElementById("myAudio");
      x.play();
    },

    movePoint(pointDuration, el = null, el2 = null) {
      const maxScore = Math.max(...this.teams.map(team => team.score));

      this.teams.forEach(team => {
        anime({
          targets: this.$refs[`${team.id}-shipOuter`],
          translateX: `${((team.score * 100) / maxScore) *
            (POINT_PERCENT / 100)}%`,
          duration: pointDuration,
          easing: "linear",
          endDelay: 3000,
          complete: anim => {
            if (el) {
              el.style.opacity = 0;
            }
            if (el2) {
              el2.style.opacity = 0;
            }
          }
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
header {
  margin: 0 3%;
  height: 20vh;
  #logo {
    width: 7%;
  }
  .br {
    margin-top: 5px;
  }
  .main-object {
    position: absolute;
    text-align: center;
    top: 2%;
  }
  .header-flex {
    display: flex;
    justify-content: space-between;
    p {
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
  width: 70vw;
  height: 80vh;
  position: relative;
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
  .team-name {
    font-size: 21px;
  }
  .score {
    font-size: 25px;
  }
}
.score-item:first-child {
  margin: 0;
}
.team-icon {
  display: inline-block;
  width: 7vh;
  height: 7vh;
  background-size: contain;
  background-position: center;
  background-repeat: no-repeat;
}
.team-pos {
  margin-left: -15px;
  font-size: 20px;
  margin-right: 10px;
}
.team--2 {
  background-image: url("~@/assets/characters/2.png");
}
.team--3 {
  background-image: url("~@/assets/characters/3.png");
}
.team--7 {
  background-image: url("~@/assets/characters/7.png");
}
.team--27 {
  background-image: url("~@/assets/characters/27.png");
}
.team--36 {
  background-image: url("~@/assets/characters/36.png");
}
.team--37 {
  background-image: url("~@/assets/characters/37.png");
}
.team--38 {
  background-image: url("~@/assets/characters/38.png");
}
.team--39 {
  background-image: url("~@/assets/characters/39.png");
}
.team--68 {
  background-image: url("~@/assets/characters/68.png");
}
.team--155 {
  background-image: url("~@/assets/characters/155.png");
}
.team7{
  margin-bottom: -8px;
}
.team36{
  margin-bottom: -13px;
}
.team39{
  margin-bottom: -5px;
}
.team27{
  margin-bottom: -8px;
}
.team37{
  margin-bottom: -8px;
}
.team68{
  width: 50px!important;
  margin-bottom: -7px;
}
.head{
  img {
    width: 12%;
    margin-right: 1vw;
  }
}
.start-line {
  position: absolute;
  left: -60px;
  margin-left: 20px;
  img {
    height: 85vh;
  }
  .start-text {
    position: absolute;
    left: -10px;
    top: 45%;
    font-size: 25px;
    transform: rotate(-90deg);
    text-transform: uppercase;
  }
}
.finish-line {
  position: absolute;
  right: -13%;
  top: 0;
  img {
    height: 85vh;
    transform: rotate(180deg);
  }
  .finish-text {
    position: absolute;
    right: -10px;
    top: 40%;
    font-size: 25px;
    transform: rotate(90deg);
    text-transform: uppercase;
    color: #fff;
  }
}
.ships {
  margin-left: 5%;
}
.gold {
  color: #d6af36;
}
.silver {
  color: #a7a7ad;
}
.bronze {
  color: #a77044;
}
</style>
