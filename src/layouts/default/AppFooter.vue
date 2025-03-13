<template>
  <v-app-footer flat class="footer">
    <v-container>
      <v-row align="center" justify="space-between">
        <!-- Esquerda: Marca/Logo -->
        <v-col cols="12" sm="4" class="d-flex align-center">
          <v-btn
            variant="text"
            class="footer-brand"
            @click="$router.push({ name: 'instances' })"
          >
            <v-img
              src="@/assets/Letschat.svg"
              height="24"
              width="24"
              class="mr-2"
            />
            <span>LETSLINK</span>
          </v-btn>
        </v-col>

        <!-- Centro: Status de Conexão -->
        <v-col cols="12" sm="4" class="d-flex justify-center">
          <div class="footer-status">
            <v-icon v-if="AppStore.connecting" color="info" size="20">
              mdi-loading mdi-spin
            </v-icon>
            <v-chip
              v-else-if="AppStore.validConnection"
              color="success"
              class="px-2 footer-chip"
              small
            >
              <div class="d-flex align-center gap-1">
                <v-icon color="success" size="16">mdi-check-circle</v-icon>
                <span class="text-truncate">
                  {{
                    AppStore.connection.host
                      .replace(/https?:\/\//, "")
                      .replace(/\/$/, "")
                  }}
                </span>
                <v-chip size="x-small" color="grey lighten-2" class="ml-1">
                  <b>{{ AppStore.version }}</b>
                </v-chip>
              </div>
            </v-chip>
            <v-icon v-else color="error" size="20">mdi-alert-circle</v-icon>
          </div>
        </v-col>

        <!-- Direita: Idioma, Configurações e Tema -->
        <v-col cols="12" sm="4" class="d-flex justify-end align-center">
          <v-menu>
            <template v-slot:activator="{ props }">
              <v-btn class="footer-btn mr-2" v-bind="props" small>
                <v-icon left size="18">mdi-translate</v-icon>
                {{ currentLanguage }}
              </v-btn>
            </template>
            <v-list :value="currentLanguage">
              <v-list-item
                v-for="lang in availableLanguages"
                :key="lang"
                @click="changei18n(lang)"
                :disabled="lang === currentLanguage"
              >
                <v-list-item-title class="text-center">{{
                  lang
                }}</v-list-item-title>
              </v-list-item>
            </v-list>
          </v-menu>
          <v-btn @click="openSettings" icon class="footer-btn mr-2" small>
            <v-icon size="18">mdi-cog</v-icon>
          </v-btn>
          <v-btn @click="toggleTheme" icon class="footer-btn" small>
            <v-icon size="18">
              mdi-{{ dark ? "white-balance-sunny" : "weather-night" }}
            </v-icon>
          </v-btn>
        </v-col>
      </v-row>
    </v-container>
    <!-- Modal de Configurações -->
    <SettingsModal ref="settings" />
  </v-app-footer>
</template>

<script>
import SettingsModal from "@/components/modal/Settings.vue";
import { useAppStore } from "@/store/app";
import { useTheme } from "vuetify";

export default {
  name: "Appfooter",
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
