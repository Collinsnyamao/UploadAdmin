<template>
  <nav class="navbar navbar-expand-lg navbar-light">
    <div class="container-fluid">
      <a class="navbar-brand">{{ routeName }}</a>
      <button class="navbar-toggler navbar-burger"
              type="button"
              @click="toggleSidebar"
              :aria-expanded="$sidebar.showSidebar"
              aria-label="Toggle navigation">
        <span class="navbar-toggler-bar"></span>
        <span class="navbar-toggler-bar"></span>
        <span class="navbar-toggler-bar"></span>
      </button>
      <div class="collapse navbar-collapse">
        <ul class="navbar-nav ml-auto">
          <!--<li class="nav-item">
            <a href="#" class="nav-link">
              <i class="ti-panel"></i>
              <p>Stats</p>
            </a>
          </li>-->
          <!--<drop-down class="nav-item"
                     title="5 Notifications"
                     title-classes="nav-link"
                     icon="ti-bell">
            <a class="dropdown-item" href="#">Notification 1</a>
            <a class="dropdown-item" href="#">Notification 2</a>
            <a class="dropdown-item" href="#">Notification 3</a>
            <a class="dropdown-item" href="#">Notification 4</a>
            <a class="dropdown-item" href="#">Another notification</a>
          </drop-down>-->
          <li class="nav-item">
            <!--<a href="#" class="nav-link">
              <i class="ti-settings"></i>
              <p>
                Settings
              </p>
            </a>-->
            <h6>
              <span class="dot" v-if="isOnline === false"></span>
              <span class="dotOffline" v-if="isOnline === true"></span>
              <span v-bind:class="{'text-success': isOnline }"> {{ onlineStatus }} </span>
            </h6>
          </li>
        </ul>
      </div>
    </div>
  </nav>
</template>
<script>

import axios from 'axios';

const url = process.env.VUE_APP_SERVER_ADDRESS;
export default {
  computed: {
    routeName() {
      const {name} = this.$route;
      return this.capitalizeFirstLetter(name);
    }
  },
  data() {
    return {
      activeNotifications: false,
      isOnline: false,
      onlineStatus: ''
    };
  },
  mounted() {
  },
  created() {
    this.pingServer();
  },
  methods: {
    capitalizeFirstLetter(string) {
      return string.charAt(0).toUpperCase() + string.slice(1);
    },
    toggleNotificationDropDown() {
      this.activeNotifications = !this.activeNotifications;
    },
    closeDropDown() {
      this.activeNotifications = false;
    },
    toggleSidebar() {
      this.$sidebar.displaySidebar(!this.$sidebar.showSidebar);
    },
    hideSidebar() {
      this.$sidebar.displaySidebar(false);
    },
    pingServer() {
      let self = this;
      axios.get(url + '/ping').then(function (response) {
        self.isOnline = true;
        self.onlineStatus = 'Online';
      }).catch(function (error) {
        self.isOnline = false;
        console.log('error', error);
        self.onlineStatus = 'Offline';
      });

      setTimeout(function () {
        self.pingServer();
      }, 1000);
    }
  }
};
</script>
<style>
.dot {
  height: 10px;
  width: 10px;
  background-color: #bbb;
  border-radius: 50%;
  display: inline-block;
}
.dotOffline {
  height: 10px;
  width: 10px;
  background-color: #4CD964;
  border-radius: 50%;
  display: inline-block;
}
</style>
