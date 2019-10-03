<template>
  <div class="container">
    <div class="main" style="z-index:1000">
      <div
        :ref="`${team.id}-shipOuter`"
        v-for="team in teams"
        :key="`ship-${team.name}`"
      >
        <div :ref="`${team.id}-shipContainer`" class="ship-container">
          <span :ref="`${team.id}-ship`" class="team-icon"> </span>
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
        <span class="score">
          {{ `${team.score}`.padStart(2, "0") }}
        </span>

        <span class="score-team">{{ team.name }}</span>
      </li>
    </transition-group>
  </div>
</template>

<script>
import anime from "animejs";
import moment from "moment";
import axios from "axios";
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
  margin-bottom: 1vh;
}

.team-list-move {
  /* applied to the element when moving */
  transition: transform 0.5s cubic-bezier(0.55, 0, 0.1, 1);
  transition: transform 1s;
}

h3 {
  margin: 40px 0 0;
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
  height: 100vh;
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
}

li {
  text-align: left;
  margin: 0 10px;
  color: white;
}

.team-icon {
  display: inline-block;
  border-radius: 50%;
  background-color: white;
  width: 5vh;
  height: 5vh;
}

.team--yoda {
  background: url("~@/assets/yoda.png");
}
</style>
