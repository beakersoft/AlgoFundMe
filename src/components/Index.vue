<template>
  <div>
    <a href="#" @click="showComonent('progress')">View Progress</a><br />
    <a href="#" @click="showComonent('donate')">Donate</a>

    <div>
      <p v-if="showDonate">
        <Donate />
      </p>

      <p v-if="showProgress">
        <Progress />
      </p>
    </div>
  </div>
</template>

<script>
import Progress from "./FundingProgress.vue";
import Donate from "./Donate.vue";
export default {
  name: "Index",
  components: {
    Progress,
    Donate,
  },
  data: function () {
    return {
      showProgress: false,
      showDonate: false,
    };
  },
  methods: {
    showComonent(type) {
      if (type === "donate") {
        this.showProgress = false;
        this.showDonate = true;
      }

      if (type === "progress") {
        this.showProgress = true;
        this.showDonate = false;
      }
    },
  },
};
</script>


