<template>
  <div>

    <!--Charts-->
    <div class="row">

      <div class="col-12">
        <div class="card">
          <div class="card-header">
            <h6>Add bank</h6>
          </div>
          <div class="card-body">

            <div class="row">
              <div class="col-4">
                <div class="card text-center">
                  <div class="card-header">
                    Checksum
                  </div>
                  <div class="card-body">
                    <h5 class="card-title">Clear checksum database</h5>
                    <p class="card-text">This will clear checksum of all ingested files.<br><br><span>This action cannot be reversed!</span>
                    </p>
                    <div class="alert alert-success" v-if="clearChecksumStatus === true">Checksum successfully
                      cleared.
                    </div>
                    <div class="alert alert-danger" v-if="clearChecksumStatus === false">Checksum failed to clear.</div>
                    <div class="alert alert-info" v-if="clearChecksumPassword === false">Check checksum password.</div>
                    <a href="#" class="btn btn-primary" v-on:click="clearDatabase">Clear database</a>
                  </div>
                  <div class="card-footer text-muted">
                    2 days ago
                  </div>
                </div>
              </div>
              <div class="col-4">
                <div class="card text-center">
                  <div class="card-header">
                    Response
                  </div>
                  <div class="card-body">
                    <h5 class="card-title">Simulate response</h5>
                    <p class="card-text">This feature will simulate a response to all currently ingested
                      files.<br><span>This action will not work if no files have been ingested recently.</span></p>
                    <div class="alert alert-danger" v-if="clearChecksumStatus === false">failed to simulate.</div>
                    <div class="alert alert-success" v-if="simulateStatus === true">Simulation passed.</div>
                    <a class="btn btn-primary" v-on:click="simulateResponse">Simulate</a>
                  </div>
                  <div class="card-footer text-muted">
                    2 days ago
                  </div>
                </div>
              </div>
              <div class="col-4">
                <div class="card text-center">
                  <div class="card-header">
                    Simulate
                  </div>
                  <div class="card-body">
                    <h5 class="card-title">Special specific response</h5><br>
                    <!--<p class="card-text">This feature will simulate a response to a specific currently ingested files.</p>
                   -->
                    <div class="row">
                      <div class="col-md-12">
                        <label for="inputGroupSelect01">Select document</label>
                        <select class="custom-select" id="inputGroupSelect01" v-model="selectedID">
                          <option v-for="document in documentsArray" :value="document.id">
                            {{ document.filename }} - {{ document.id }} - {{ document.timeAgo }} ago.
                          </option>
                        </select>
                      </div>
                    </div>
                    <div class="alert alert-danger" v-if="simulateOneStatus === false">failed to simulate.</div>
                    <div class="alert alert-success" v-if="simulateOneStatus === true">Simulation passed.</div>
                    <div class="alert alert-danger" v-if="emptyInput === true">Empty input</div>
                    <br>
                    <a class="btn btn-primary" v-on:click="simulateOneResponse">Simulate One</a>
                  </div>
                  <div class="card-footer text-muted">
                    2 days ago
                  </div>
                </div>
              </div>
            </div>

            <div class="clearfix"></div>


          </div>
        </div>
      </div>

    </div>

  </div>
</template>
<script>
import EditProfileForm from "./UserProfile/EditProfileForm.vue";
import UserCard from "./UserProfile/UserCard.vue";
import MembersCard from "./UserProfile/MembersCard.vue";
import axios from "axios";

const url = process.env.VUE_APP_SERVER_ADDRESS;
export default {
  components: {
    EditProfileForm,
    UserCard,
    MembersCard
  },
  data() {
    return {
      documentsArray: [],
      clearChecksumStatus: '',
      simulateStatus: '',
      simulateOneStatus: '',
      emptyInput: '',
      clearChecksumPassword: true,
      selectedID: ''
    };
  },
  mounted() {
    let self = this;
    axios.post(url+"/upload/checksum", {})
      .then(function (response) {
        console.log(response);
        response.data.forEach(function (obj) {

          let date = obj.dateTime;
          date = Number(date);
          let newDate = new Date(date);
          let interval = new Date() - newDate;
          let intervalFormatted = new Date(date);

          let minutes = Math.floor(interval / 60000);
          let seconds = ((interval % 60000) / 1000).toFixed(0);
          let mins = minutes + " mins " + (seconds < 10 ? '0' : '') + seconds + ' seconds ';
          let localDate = newDate.toLocaleDateString();
          let user = obj.user;
          let filename = obj.filename.replace('*', '');
          let checksum = obj.checksum;
          let extension = obj.extension;
          let status = obj.status;
          let id = obj._id;

          self.documentsArray.push({id: id, filename: filename, timeAgo: mins});
        })
      })
      .catch(function (error) {
        console.log(error);
      });
  },
  methods: {
    clearDatabase() {
      let self = this;
      axios.post(url + '/upload/clearDB', {
        password: 'tintin'
      })
        .then(function (response) {
          console.log(response);
          self.clearChecksumStatus = true;

        }).catch(function (error) {
        console.log(error);
        self.clearChecksumStatus = false;
      })
    },
    simulateResponse() {
      let self = this;
      axios.post(url + '/upload/simulate', {
      })
        .then(function (response) {
          console.log(response);
          self.simulateStatus = true;

        }).catch(function (error) {
        console.log(error);
        self.simulateStatus = false;
      })
    },
    simulateOneResponse() {
      let self = this;
      console.log(this.selectedID);
      if (this.selectedID){
        axios.post(url + '/upload/simulateOne', {
          id: this.selectedID
        })
          .then(function (response) {
            console.log(response);
            self.simulateOneStatus = true;

          }).catch(function (error) {
          console.log(error);
          self.simulateOneStatus = false;
        })
      }else {
        this.emptyInput = true;
      }

    }
  },
};
</script>
<style>
</style>
