<template>
    <div class="row">
      <div class="col-12">
        <card :title="title" :subTitle="subtitle">
          <div slot="raw-content" class="table-responsive">
            <paper-table :data="checksumArray" :columns="tableColumns2" type="hover">

            </paper-table>
          </div>
        </card>
      </div>

      <!--<div class="col-12">
        <card class="card-plain">
          <div class="table-full-width table-responsive">
            <paper-table :data="checksumArray" :columns="tableColumns2" type="hover">
            </paper-table>
          </div>
        </card>
      </div>-->

    </div>
</template>
<script>
import { PaperTable } from "@/components";
import axios from 'axios'
import VueFilterDateFormat from 'vue-filter-date-format';

const url = process.env.VUE_APP_SERVER_ADDRESS;
export default {
  components: {
    PaperTable,
    VueFilterDateFormat
  },
  data() {
    return {
      tableColumns2: ["id", "user", "filename", "status", "extension", "date"],
      checksumArray: [],
      title: 'Checksum table',
      subtitle: 'Checksum table',
      /*table1: {
        title: "Stripped Table",
        subTitle: "Here is a subtitle for this table",
        columns: [...this.tableColumns2],
        data: [...checksumArray]
      },*/
      /*table2: {
        title: "Table on Plain Background",
        subTitle: "Here is a subtitle for this table",
        columns: [...tableColumns2],
        data: [...checksumArray]
      }*/
    };
  },
  methods : {

  },
  mounted() {
    let self = this;
    updateTable();
    function updateTable () {
      axios.post(url + "/upload/checksum", {
      })
        .then(function (response) {
          console.log(response);
          response.data.forEach(function (obj) {
            if (obj.user === ''){
              obj.user = 'n/a';
            }
            if (obj.status === true){
              obj.status = 'completed';
            }else if (obj.status === false){
              obj.status = 'Not completed';
            }

            let date = obj.dateTime;
            date = Number(date);
            let newDate = new Date(date);
            console.log(newDate.getFullYear());
            let localDate = newDate.toLocaleDateString();
            let time = newDate.toLocaleTimeString('en-US');
            let user = obj.user;
            let filename = obj.filename.replace('*','');
            let checksum = obj.checksum;
            let extension = obj.extension;
            let status = obj.status;
            let id = obj._id;

            self.checksumArray.push({id: id, user: user, filename: filename, status: status,extension: extension, date: localDate + ' ' + time})

          })
          console.log(self.checksumArray);
        })
        .catch(function (error) {
          console.log(error);
        });
    }
  }
};
</script>
<style>
</style>
