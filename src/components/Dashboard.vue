<template>
  <div class="container">
    <div class="main">
      kjadkasjd
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
          name: "team 1",
          score: 5
        },
        {
          name: "team 2",
          score: 6
        },
        {
          name: "team 3",
          score: 7
        },
        {
          name: "team 4",
          score: 8
        },
        {
          name: "team 5",
          score: 6
        },
        {
          name: "team 6",
          score: 3
        },
        {
          name: "team 7",
          score: 2
        },
        {
          name: "team 8",
          score: 4
        },
        {
          name: "team 9",
          score: 0
        },
        {
          name: "team 10",
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
.container {
  display: flex;
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
  font-size: 3vh;
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
