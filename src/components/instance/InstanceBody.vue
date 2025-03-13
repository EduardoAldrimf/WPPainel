<template>
  <v-tabs v-model="tab" background-color="transparent" color="primary" grow>
    <v-tab v-for="tab in tabs" :key="tab.id" :value="tab.id">
      <v-icon start>{{ tab.icon }}</v-icon>
      {{ $t(`instanceTabs.${tab.id}`) }}
    </v-tab>
  </v-tabs>

  <v-window v-model="tab">
    <v-window-item v-for="tab in tabs" :key="tab.id" :value="tab.id">
      <div class="d-flex flex-column gap-6">
        <component
          v-for="component in tab.components"
          :key="component"
          :is="component"
          :instance="instance"
        />
      </div>
    </v-window-item>
  </v-window>
</template>

<script>
import Options from "./settings/Options.vue";

import OpenSendMessage from "./message/OpenSendMessage.vue";
import MyGroups from "./message/MyGroups.vue";
import MyChats from "./message/MyChats.vue";
import MyContacts from "./message/MyContacts.vue";
import HasWhatsapp from "./message/HasWhatsapp.vue";

import ConnectionAlert from "./profile/ConnectionAlert.vue";
import BasicInfo from "./profile/BasicInfo.vue";
import Privacy from "./profile/Privacy.vue";
import ProfilePhoto from "./profile/ProfilePhoto.vue";
export default {
  components: {
    Options,
    OpenSendMessage,
    MyGroups,
    MyChats,
    HasWhatsapp,
    MyContacts,
    ConnectionAlert,
    BasicInfo,
    Privacy,
    ProfilePhoto,
  },
  data: () => ({
    tab: "settings",
    tabs: [
      {
        id: "settings",
        icon: "mdi-cog",
        components: ["Options"],
      },
      {
        id: "message",
        icon: "mdi-message",
        components: [
          "OpenSendMessage",
          "HasWhatsapp",
          "MyContacts",
          "MyGroups",
          "MyChats",
        ],
      },
      {
        id: "profile",
        icon: "mdi-account",
        components: ["ConnectionAlert", "BasicInfo", "ProfilePhoto", "Privacy"],
      },
    ],
  }),
  props: {
    instance: {
      type: Object,
      required: true,
    },
  },
};
</script>

<style></style>
