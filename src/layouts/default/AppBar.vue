<template></template>

<script>
import SettingsModal from "@/components/modal/Settings.vue";
import { useAppStore } from "@/store/app";
import { useTheme } from "vuetify";

export default {
  name: "AppBar",
  data: () => ({
    AppStore: useAppStore(),
    theme: useTheme(),
  }),
  components: {
    SettingsModal,
  },
  methods: {
    changei18n(locale) {
      this.$vuetify.locale.current = locale;
      window.localStorage.setItem("locale", locale);
    },
    toggleTheme() {
      const theme = this.theme.global.current.dark ? "light" : "dark";
      this.theme.global.name = theme;
      localStorage.setItem("theme", theme);
    },
    openSettings() {
      this.$refs.settings.open();
    },
    async loadConnectionFromUrl() {
      try {
        const { connection } = this.$route.query;
        if (!connection) return null;
        this.$router.replace({ query: {} });

        const json = atob(connection);
        const data = JSON.parse(json);
        if (!data.host || !data.globalApiKey) return null;

        await this.AppStore.setConnection(data);
        return data;
      } catch (e) {
        console.error(e);
        return null;
      }
    },
  },
  computed: {
    dark() {
      return this.theme.global.current.dark;
    },
    availableLanguages() {
      return this.$i18n.availableLocales;
    },
    currentLanguage() {
      return this.$i18n.locale;
    },
  },
  async mounted() {
    try {
      const urlConnection = await this.loadConnectionFromUrl();
      if (!urlConnection) await this.AppStore.loadConnection();
    } catch (e) {
      console.error(e);
    } finally {
      if (!this.AppStore.validConnection) this.openSettings();
    }
  },
};
</script>
