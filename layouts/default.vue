<template>
  <v-app>
    <v-toolbar light dense color='white' class='top-toolbar'>
      <v-menu v-for='button in buttons' :close-on-content-click="content_click_option">
        <template #activator="{ on: menu }" v-show='button.options'>
          <v-tooltip bottom>
            <template #activator="{ on: tooltip }">
              <v-btn color="info" icon small flat v-on="{ ...tooltip, ...menu }">
                <v-icon>{{ button.icon }}</v-icon>
              </v-btn>
            </template>
            <span>{{ button.title }}</span>
          </v-tooltip>
        </template>
        <temmplate v-if="content_click_option" #activator="{ on: tooltip }" v-show='!button.options'>
          <v-tooltip bottom>
            <template #activator="{ on: tooltip }">
              <v-btn color="info" icon small flat v-on="{ ...tooltip }">
                <v-icon>{{ button.icon }}</v-icon>
              </v-btn>
            </template>
            <span>{{ button.title }}</span>
          </v-tooltip>
        </temmplate>
        <v-list v-show='button.options'>
          <v-list-tile v-for="(item, index) in button.options" :key="item.id"
            @click="item.options ? content_click_option = false : content_click_option = true">
            <v-list-tile-content>
              <v-list-tile-title v-html="item.title"></v-list-tile-title>

            </v-list-tile-content>
            <v-list-tile-action v-if='item.options'>

              <v-menu v-model="menu" :close-on-content-click="false" :nudge-width="200" offset-x>
                <template v-slot:activator="{ on }">

                  <v-icon v-on="on" color='info' small @click="menu = true">fa-chevron-right</v-icon>
                </template>

                <v-card>
                  <v-list>
                    <v-list-tile avatar>
                      <v-list-tile-avatar>
                        <img src="https://cdn.vuetifyjs.com/images/john.jpg" alt="John">
                      </v-list-tile-avatar>

                      <v-list-tile-content>
                        <v-list-tile-title>John Leider</v-list-tile-title>
                        <v-list-tile-sub-title>Founder of Vuetify.js</v-list-tile-sub-title>
                      </v-list-tile-content>

                      <v-list-tile-action>
                        <v-btn :class="fav ? 'red--text' : ''" icon @click="fav = !fav">
                          <v-icon>favorite</v-icon>
                        </v-btn>
                      </v-list-tile-action>
                    </v-list-tile>
                  </v-list>

                  <v-divider></v-divider>

                  <v-list>
                    <v-list-tile>
                      <v-list-tile-action>
                        <v-switch v-model="message" color="purple"></v-switch>
                      </v-list-tile-action>
                      <v-list-tile-title>Enable messages</v-list-tile-title>
                    </v-list-tile>

                    <v-list-tile>
                      <v-list-tile-action>
                        <v-switch v-model="hints" color="purple"></v-switch>
                      </v-list-tile-action>
                      <v-list-tile-title>Enable hints</v-list-tile-title>
                    </v-list-tile>
                  </v-list>

                  <v-card-actions>
                    <v-spacer></v-spacer>

                    <v-btn flat @click="menu = false">Cancel</v-btn>
                    <v-btn color="primary" flat @click="menu = false;">Save</v-btn>
                  </v-card-actions>
                </v-card>
              </v-menu>
            </v-list-tile-action>


          </v-list-tile>
        </v-list>
      </v-menu>

    </v-toolbar>
    <nuxt />
  </v-app>
</template>

<script>
export default {
  name: 'DefaultLayout',
  data() {
    return {
      clipped: false,
      drawer: false,
      fixed: false,
      miniVariant: false,
      right: true,
      rightDrawer: false,
      title: 'Vuetify.js',
      drawer: false,
      group: null,
      fav: true,
      menu: false,
      message: false,
      hints: true,
      content_click_option: false,
      buttons: [
        {
          id: 'options',
          title: 'More Options',
          icon: 'fas fa-bars',
          action: '',
          options: [
            { id: 'dictionary', title: 'Dictionary', action: '' },
            { id: 'visualization', title: 'Data Visualization', action: '' },
            { id: 'password', title: 'Change Password', action: '' },
            { id: 'license', title: 'License Info', action: '' },
            {
              id: 'tools', title: 'Tools', action: '',
              options: [
                { id: 'calculator', title: 'Hex to ASCII calculator' }
              ]
            },
            { id: 'checkup', title: 'Checkup Report', action: '' },
            { id: 'system', title: 'System Monitoring', action: '' },
            { id: 'db', title: 'Database Management', action: '' },
          ]
        },
        { id: 'reports', title: 'Reports', icon: 'fas fa-chart-line', action: '' },
        {
          id: 'help',
          title: 'Help Options',
          icon: 'fas fa-question-circle',
          action: '',
          options: [
            { id: 'user', title: 'User Guide', action: '' },
            { id: 'admin', title: 'Admin Guide', action: '' },
          ]
        },
        { id: 'settings', title: 'Settings', icon: 'fas fa-cog', action: '' },
        { id: 'logout', title: 'Logout', icon: 'fas fa-power-off', action: '' },
      ]
    }

  }
}
</script>
