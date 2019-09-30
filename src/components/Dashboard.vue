<template>
  <div class="container">
    <div class="main" style="z-index:1000">
      <div class="team-icon" style="border-radius:50%; background-color: white; width:5vh; height:5vh">

      </div>
      <div class="team-icon" style="border-radius:50%; background-color: white; width:5vh; height:5vh">

      </div>
      <div class="team-icon" style="border-radius:50%; background-color: white; width:5vh; height:5vh">

      </div>
      <div class="team-icon" style="border-radius:50%; background-color: white; width:5vh; height:5vh">

      </div>
    </div>
    <transition-group name="team-list" tag="ul" class="scoreboard">
      <li v-for="team in sortedTeams" :key="team.name">
        {{ `${team.score}`.padStart(2, "0") }} {{ team.name }}
      </li>
    </transition-group>
  </div>
</template>

<script>
export default {
  name: "Dashboard",
  props: {
    msg: String
  },
  data() {
    return {
      teams: [
        {
          name: "team mouse",
          score: 5
        },
        {
          name: "team cow",
          score: 6
        },
        {
          name: "team tiger",
          score: 7
        },
        {
          name: "team rabbit",
          score: 8
        },
        {
          name: "team dragon",
          score: 6
        },
        {
          name: "team snake",
          score: 3
        },
        {
          name: "team horse",
          score: 2
        },
        {
          name: "team sheep",
          score: 4
        },
        {
          name: "team monkey",
          score: 0
        },
        {
          name: "team chicken",
          score: 0
        }
      ]
    };
  },
  computed: {
    sortedTeams() {
      return this.teams.concat().sort((a, b) => b.score - a.score);
    }
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style lang="scss" scoped>
.team-icon {
  transition: all 2s;

  transform: translateX(400px);
}

.container {
  display: flex;
  z-index: 100;
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
</style>
