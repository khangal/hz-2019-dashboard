<template>
  <div class="container">
    <div class="main" style="z-index:1000">
      <div
        :ref="`${team.id}-shipOuter`"
        v-for="team in teams"
        :key="`ship-${team.name}`"
      >
        <div :ref="`${team.id}-shipContainer`" class="ship-container">
          <span :ref="`${team.id}-ship`" class="team-icon"></span>
        </div>
      </div>
    </div>
    <transition-group name="team-list" tag="ul" class="scoreboard" style="z-index:10000">
      <li v-for="team in sortedTeams" :key="team.name" @click="boost(team.id, 200)">
        {{ `${team.score}`.padStart(2, "0") }} {{ team.name }}
      </li>
    </transition-group>
  </div>
</template>

<script>
import anime from "animejs";
import moment from "moment";

// const duration = 10 * 3600 * 5
const duration = 1000 * 3600 * 5;
const startTime = moment("2019-10-01 09:10");

const TIME_PERCENT = 50;
const POINT_PERCENT = 50;
const TOTAL_POINTS = 1600;
const oneScoreValue = POINT_PERCENT / TOTAL_POINTS;

export default {
  name: "Dashboard",
  props: {
    msg: String
  },
  data() {
    return {
      teams: [
        {
          id: 1,
          name: "team mouse",
          score: 5 * 200
        },
        {
          id: 2,
          name: "team cow",
          score: 6 * 200
        },
        {
          id: 3,
          name: "team tiger",
          score: 7 * 200
        },
        {
          id: 4,
          name: "team rabbit",
          score: 8 * 200
        },
        {
          id: 5,
          name: "team dragon",
          score: 6 * 200
        },
        {
          id: 6,
          name: "team snake",
          score: 3 * 200
        },
        {
          id: 7,
          name: "team horse",
          score: 2 * 200
        },
        {
          id: 8,
          name: "team sheep",
          score: 4 * 200
        },
        {
          id: 9,
          name: "team monkey",
          score: 0 * 200
        },
        {
          id: 10,
          name: "team chicken",
          score: 0 * 200
        }
      ]
    };
  },
  mounted() {
    this.go();
  },
  computed: {
    sortedTeams() {
      return this.teams.concat().sort((a, b) => b.score - a.score);
    }
  },
  methods: {
    go() {
      let progress = moment.duration(moment().diff(startTime)).asMilliseconds();
      const seekProgress = (progress * 100) / duration;

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

      this.teams.forEach(team => {
        const scoreAnimation = anime({
          targets: this.$refs[`${team.id}-shipOuter`],
          translateX: `${oneScoreValue * team.score}%`,
          duration: 0,
          easing: "linear"
        });
      });
    },
    boost(teamId, score) {
      const team = this.teams.find(team => team.id === teamId)
      team.score += score

      anime({
        targets: this.$refs[`${teamId}-shipOuter`],
        translateX: `${oneScoreValue * team.score}%`,
        duration: 3000,
        easing: "linear"
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
  width: 80vw;
  height: 100vh;
}

.scoreboard {
  font-size: 32px;
  margin-left: auto;
  max-width: 20vw;
  list-style-type: none;
  padding: 0;
  margin-right: 2vw;
}

li {
  text-align: left;
  margin: 0 10px;
}

a {
  color: #42b983;
}

.team-icon {
  display: inline-block;
  border-radius: 50%;
  background-color: white;
  width: 5vh;
  height: 5vh;
}
</style>
