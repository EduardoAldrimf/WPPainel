<template>
</template>

<script>
import Contribute from "./Contribute.vue";
import { version } from "../../../package.json";
export default {
  name: "SettingsModal",
  data: () => ({
    dialog: false,
    version,
    isHttps: window.location.protocol === "https:",
  }),
  methods: {
    contribute() {
      this.$refs.contribute.open();
    },
    open() {
      this.dialog = true;
    },
  },
  components: { Contribute },
};
</script>
