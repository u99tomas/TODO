<template>
  <div class="dashboard">
    <h1 class="subtitle-1 grey--text ">Dashboard</h1>
    <v-container class="my-5">
      <v-row class="mb-10">

        <v-tooltip top>
          <template v-slot:activator="{ on, attrs }">
            <v-btn
              small
              text
              color="grey"
              v-bind="attrs"
              v-on="on"
              @click="sortBy('title')"
            >
              <v-icon left small>mdi-folder</v-icon>
              <span class="caption text-lowercase">By project name</span>
            </v-btn>
          </template>
          <span>Sort projects by project name</span>
        </v-tooltip>

        <v-tooltip top>
          <template v-slot:activator="{ on, attrs }">
            <v-btn
              small
              text
              color="grey"
              v-bind="attrs"
              v-on="on"
              @click="sortBy('person')"
            >
              <v-icon left small>mdi-account</v-icon>
              <span class="caption text-lowercase">By person</span>
            </v-btn>
          </template>
          <span>Sort projects by person</span>
        </v-tooltip>

      </v-row>

      <v-card
        flat
        :class="`project ${project.status}`"
        v-for="project in projects"
        :key="project.id"
      >
        <v-row class="pa-3">
          <v-col xs="12" md="6">
            <div class="caption">Project title</div>
            <div>{{ project.title }}</div>
          </v-col>

          <v-col xs="6" sm="4" md="2">
            <div class="caption">Person</div>
            <div>{{ project.person }}</div>
          </v-col>

          <v-col xs="6" sm="4" md="2">
            <div class="caption">Due by</div>
            <div>{{ project.due }}</div>
          </v-col>

          <v-col xs="2" sm="4" md="2">
            <div class="float-right">
              <v-chip
                small
                :class="`${project.status} white--text caption my-2`"
                >{{ project.status }}</v-chip
              >
            </div>
          </v-col>
        </v-row>
      </v-card>
    </v-container>
  </div>
</template>

<script>
import db from "../firebase.js";

export default {
  name: "Dashboard",
  data() {
    return {
      projects: [],
    };
  },
  methods: {
    sortBy(prop) {
      this.projects.sort((a, b) => (a[prop] < b[prop] ? -1 : 1));
    },
  },
  created(){
    db.collection('projects').onSnapshot(res =>{
      const changes = res.docChanges();
      changes.forEach(change => {
        if (change.type === "added"){
          this.projects.push({
            ...change.doc.data(),
            id : change.doc.id
          });
        }
      });
    });
  }
};
</script>

<style scoped>
.project.complete {
  border-left: 4px solid #3cd1c2;
}
.project.ongoing {
  border-left: 4px solid orange;
}
.project.overdue {
  border-left: 4px solid tomato;
}
.v-chip.complete {
  background: #3cd1c2 !important;
}
.v-chip.ongoing {
  background: #ffaa2c !important;
}
.v-chip.overdue {
  background: #f83e70 !important;
}
</style>
