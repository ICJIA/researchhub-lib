<template>
  <div>
    <v-app-bar
      id="toolbar"
      app
      color="white"
      :elevation="elevation"
      fixed
      :height="height"
    >
      <a
        class="hidden-xs-only"
        href="http://www.icjia.state.il.us"
        target="_blank"
        rel="noreferrer"
      >
        <img :src="logoPath" :height="logoHeight" alt="logo" />
      </a>

      <router-link to="/">
        <v-toolbar-title>
          <span
            class="font-weight-bold text-uppercase"
            style="letter-spacing: 0.03em"
          >
            <template>{{ 'Research Hub' }}</template>
          </span>
          <slot name="titleExtra"></slot>
        </v-toolbar-title>
      </router-link>

      <v-spacer></v-spacer>

      <v-toolbar-items class="hidden-sm-and-down">
        <slot name="toolbarItems"></slot>
      </v-toolbar-items>

      <v-btn
        v-if="menu"
        class="hidden-md-and-up"
        icon
        text
        @click="drawer = !drawer"
      >
        <v-icon>$vuetify.icons.menu</v-icon>
      </v-btn>
    </v-app-bar>

    <v-navigation-drawer
      id="nav-drawer"
      v-model="drawer"
      app
      right
      temporary
      width="175"
    >
      <v-list>
        <slot name="toolbarDrawerItems"></slot>
      </v-list>
    </v-navigation-drawer>
  </div>
</template>

<script>
export default {
  props: {
    menu: {
      type: Boolean,
      default: true
    },
    logoPath: {
      type: String,
      default: ''
    }
  },
  data() {
    return {
      drawer: null,
      elevation: 0,
      height: 60
    }
  },
  computed: {
    logoHeight() {
      return this.height * 0.75
    }
  },
  created() {
    window.addEventListener('scroll', this.setElevation)
  },
  destroyed() {
    window.removeEventListener('scroll', this.setElevation)
  },
  methods: {
    setElevation() {
      const elevation = window.scrollY === 0 ? 0 : 1
      if (this.elevation !== elevation) this.elevation = elevation
    }
  }
}
</script>
