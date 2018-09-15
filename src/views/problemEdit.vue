<template>
  <v-container>
    <v-flex>
      <v-card flat>
        <v-progress-linear indeterminate v-if="showProgressBar"/>
        <v-card-title v-if="loaded">
          <div>
            <div class="headline" v-text="$t('edit_problem')"/>
          </div>
        </v-card-title>
        <v-card-text>
          <v-text-field :label="$t('title')" v-model="problem.title"/>
          <v-textarea :label="$t('content')" v-model="problem.content"/>
          <v-combobox v-model="problem.tags" :label="$t('tags')" hide-selected multiple chips clearable/>
          <v-divider/>
          <h4 style="color: grey" v-text="$t('data_config')"/>
          <JsonEditor :objData="problem.data" v-model="problem.data"/>
          <v-divider/>
          <h4 style="color: grey" v-text="$t('meta')"/>
          <JsonEditor :objData="problem.meta" v-model="problem.meta"/>
        </v-card-text>
        <v-card-actions>
          <v-spacer/>
          <v-btn flat color="primary" v-text="$t('save')" @click="save"/>
        </v-card-actions>
      </v-card>
    </v-flex>
    <v-snackbar v-model="showSnackbar">
      {{ snackbarText }}
    </v-snackbar>
  </v-container>
</template>

<script>
import { getURL, post, get } from "../httphelper";

export default {
  name: "problemEdit",
  props: {
    id: String
  },
  data() {
    return {
      problem: {
        title: "",
        content: "",
        data: {
          version: "1.0"
        },
        meta: {
          version: "1.0"
        },
        tags: []
      },
      showProgressBar: false,
      loaded: false,
      showSnackbar: false,
      snackbarText: ""
    };
  },
  async created() {
    if (this.id) {
      const fetchURL = getURL(`/api/problem/${this.id}`, {});
      this.problem = await get(fetchURL);
    }
    this.loaded = true;
  },
  methods: {
    async save() {
      this.showProgressBar = true;
      let updateURL = null;
      if (this.id) {
        updateURL = getURL(`/api/problem/${this.id}`, {});
      } else {
        updateURL = getURL(`/api/problem/new`, {});
      }
      const result = await post(updateURL, this.problem);
      this.showProgressBar = false;
      if (!this.id) {
        this.$router.push(`/problem/edit/${result}`);
      } else {
        this.showSnackbar = true;
        this.snackbarText = this.$t("problem_updated");
      }
    }
  }
};
</script>