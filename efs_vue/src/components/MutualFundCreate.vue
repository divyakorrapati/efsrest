<template>
  <v-container grid-list-md>
    <v-layout row wrap align-left justify-left fill-height>
      <v-flex xs12 sm8 lg7 md5>
         <v-layout column align-center>
       <v-flex xs6 sm8 md7>

         <v-alert v-if="showMsg === 'error'"
                dismissible
        :value="true"
        type="error"
      >
            Please verify MutualFund information.
      </v-alert>
       </v-flex>
         </v-layout>
        <v-card class="login-card">
          <v-card-title>
            <span class="headline">{{pageTitle}}</span>
          </v-card-title>

          <v-spacer/>

          <v-card-text>


            <v-form ref="form" lazy-validation>
              <v-container>

                <v-text-field
                  v-model="mutualfund.customer"
                  label="Customer"
                  required
                  type="number"
                />

                <v-text-field
                  v-model="mutualfund.name"
                  label="Name"
                  required
                />
                <v-text-field
                  v-model="mutualfund.description"
                  label="Description"
                  required
                />
                <v-text-field
                  v-model="mutualfund.purchased_value"
                  label="Purchased Value"
                  required
                  type="number"
                />
                <v-text-field
                  v-model="mutualfund.purchased_date"
                  label="Purchased_date"
                  required
                  type="date"

                />
              </v-container>
              <v-btn v-if="!isUpdate" class="blue white--text" @click="createMutualFund">Save</v-btn>
              <v-btn v-if="isUpdate" class="blue white--text" @click="updateMutualFund">Update</v-btn>
              <v-btn class="white black--text" @click="cancelOperation">Cancel</v-btn>


            </v-form>
          </v-card-text>
        </v-card>
      </v-flex>
    </v-layout>
  </v-container>
</template>


<script>
  import router from '../router';
  import {APIService} from '../http/APIService';
  const apiService = new APIService();

  export default {
    name: 'MutualFundCreate',
    components: {},
    data() {
      return {
        showError: false,
        mutualfund: {},
        pageTitle: "Add New MutualFund",
        isUpdate: false,
        showMsg: '',
      };
    },
    methods: {
      createMutualFund() {
        apiService.addNewMutualFund(this.mutualfund).then(response => {
          if (response.status === 201) {
            this.mutualfund = response.data;
             this.showMsg = "";
            router.push('/mutualfund-list/new');
          }else{
              this.showMsg = "error";
          }
        }).catch(error => {
          if (error.response.status === 401) {
            router.push("/auth");
          }else if (error.response.status === 400) {
            this.showMsg = "error";
          }
        });
      },
      cancelOperation(){
         router.push("/mutualfund-list");
      },
      updateMutualFund() {
        apiService.updateMutualFund(this.mutualfund).then(response => {
          if (response.status === 200) {
            this.mutualfund = response.data;
            router.push('/mutualfund-list/update');
          }else{
              this.showMsg = "error";
          }
        }).catch(error => {
          if (error.response.status === 401) {
            router.push("/auth");
          }else if (error.response.status === 400) {
            this.showMsg = "error";
          }
        });
      }
    },
    mounted() {
      if (this.$route.params.pk) {
        this.pageTitle = "Edit MutualFund";
        this.isUpdate = true;
        apiService.getMutualFund(this.$route.params.pk).then(response => {
          this.mutualfund = response.data;
        }).catch(error => {
          if (error.response.status === 401) {
            router.push("/auth");
          }
        });
      }
    },
  }
</script>
<style scoped>
  .aform {
    margin-left: auto;
    width: 60%;
  }
</style>
