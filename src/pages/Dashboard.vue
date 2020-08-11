<template>
  <div>

    <!--Charts-->
    <div class="row">

      <div class="col-8">
        <div class="card">
          <div class="card-header">
            <h6>Add bank</h6>
          </div>
          <div class="card-body">

            <div class="alert alert-success" v-if="createStatus === true">bank created successfully!</div>
            <div class="alert alert-danger" v-if="createStatus === false">bank failed to create!</div>
            <div class="row">
              <div class="col-md-12">
                <fg-input type="text"
                          label="Bank name"
                          placeholder="Example bank."
                          v-model="newBank">
                </fg-input>
              </div>
            </div>

            <div class="text-center">
              <p-button type="info"
                        round
                        @click.native.prevent="updateBanks">
                Update
              </p-button>
            </div>
            <div class="clearfix"></div>



          </div>
        </div>
        <div class="card">
          <div class="card-header">
            <h6>Delete bank</h6>
          </div>
          <div class="card-body">
            <div class="alert alert-danger" v-if="deleteStatus === false">Bank failed to delete</div>
            <div class="alert alert-success" v-if="deleteStatus === true">Bank deleted successfully</div>

            <div class="row">
              <div class="col-md-12">
                <fg-input type="text"
                          label="Bank name"
                          placeholder="Example bank."
                          v-model="deleteBank">
                </fg-input>
              </div>
            </div>

            <div class="text-center">
              <p-button type="info"
                        round
                        @click.native.prevent="deleteOneBank">
                Update
              </p-button>
            </div>
            <div class="clearfix"></div>



          </div>
        </div>
      </div>

      <div class="col-md-4">
        <div class="card">
          <div class="card-header">
            <h6>Bank list</h6>
          </div>
          <div class="card-body">
            <ul class="list-group list-group-flush">
              <li class="list-group-item" v-for="bank in banks" :key="bank._id">
                {{ bank.bankName }}
              </li>
            </ul>
          </div>
        </div>
      </div>

    </div>

  </div>
</template>
<script>
import { StatsCard, ChartCard } from "@/components/index";
import Chartist from 'chartist';

import axios from 'axios'
import VueAxios from 'vue-axios'

const url = process.env.VUE_APP_SERVER_ADDRESS;
export default {
  components: {
    StatsCard,
    ChartCard
  },
  /**
   * Chart data used to render stats, charts. Should be replaced with server data
   */
  data() {
    return {
      banks: [],
      newBank: '',
      deleteBank: '',
      createStatus: '',
      deleteStatus: ''

    };
  },
  methods: {
    updateBanks (){
      let self = this;
      if (this.newBank){
        axios.post(url + "/banks/new", {
          bankname: this.newBank
        })
          .then(function (response) {
            /*console.log(response);*/
            self.createStatus = true;
            self.banks.push(response.data);
          })
          .catch(function (error) {
            console.log(error);
            self.createStatus = false;
          });
      }
    },
    updateBankList (){
      axios.get(url + "/banks/list")
        .then(response => {
          let self = this;
          let banksArray = [];

          response.data.forEach(function (obj) {
            /*console.log(obj);*/
            banksArray.push(obj);
            self.banks = banksArray;
          })
        })
    },
    deleteOneBank (){
      let self = this;
      if (this.deleteBank){
        axios.post(url + "/banks/delete", {
          bankname: this.deleteBank
        })
          .then(function (response) {
            /*console.log(response);*/

            self.deleteStatus = true;

            for (let i = 0; i < self.banks.length; i++) {
              if (self.banks[i].bankName === self.deleteBank){
                self.banks.splice(i, 1);
              }
            }
          })
          .catch(function (error) {
            console.log(error);
            self.deleteStatus = false;
          });
      }
    }
  },
  mounted() {
    axios.get(url + "/banks/list")
      .then(response => {
        let self = this;
        let banksArray = [];

        response.data.forEach(function (obj) {
          console.log(obj);
          banksArray.push(obj);
          self.banks = banksArray;
        })
      })
  }
};
</script>
<style>
</style>
