<template>
    <v-app>
    <v-container>
    <v-row
      class="mb-8"
      no-gutters
    >
      <v-col md="5" offset-md="0">
          <h1>join the <strong><span style = "font-size:1.75em; color:#1260cc;">fun</span></strong>.</h1>
          <v-card class = "logincard">
            <v-form
            ref="form"
            v-model="valid"
            lazy-validation
        >
            <v-text-field
            v-model="emailbox"
            :rules="emailRules"
            label="E-mail"
            required
            ></v-text-field>

            <v-text-field
            v-model="passwordbox"
            :rules="passwordRules"
            label="Password"
            required
            :append-icon= "pass1 ? 'mdi-eye-outline' : 'mdi-eye-off-outline'"
            @click:append= "() => (pass1 = !pass1)"
            :type="pass1 ? 'password' : 'text'"
            ></v-text-field>

            <v-btn
            color="info" block @click="loginUser"
            :disabled="!valid"
            >
            log in
            </v-btn>
            <v-alert
              :value="loginalert"
              type ="success"
              dense
              prominent
              dismissable
              transition="scale-transition"
              id = "loginalert"
            >
            Success. Logging you in...
            </v-alert>
            <v-alert
              :value="loginalertbad"
              type ="error"
              dense
              prominent
              dismissable
              transition="scale-transition"
              id = "loginalertbad"
            >
            Error. Username or password incorrect.
            </v-alert>
        </v-form>
        </v-card>
      </v-col>
      <v-divider
      class = "mx-16"
        vertical
      ></v-divider>
      <v-col
        md="5"
        offset-md="0"
      >
      <v-card class = "logincard">
          <h2 style = "text-align: center;">Don't have an account?</h2>
            <v-form
            ref="form1"
            v-model="valid1"
            lazy-validation
        >
            <v-text-field
            v-model="namebox"
            :counter="10"
            :rules="nameRules"
            label="Name"
            required
            ></v-text-field>

            <v-text-field
            v-model="emailbox1"
            :rules="emailRules"
            label="E-mail"
            required
            ></v-text-field>

            <v-text-field
            v-model="passwordbox1"
            :rules="passwordRules"
            label="Password"
            required
            :append-icon= "pass ? 'mdi-eye-outline' : 'mdi-eye-off-outline'"
            @click:append= "() => (pass = !pass)"
            :type="pass ? 'password' : 'text'"
            ></v-text-field>

            <v-checkbox
            v-model="checkbox"
            :rules="[v => !!v || 'You must agree to continue!']"
            label="Do you agree to our terms and agreements?"
            required
            ></v-checkbox>

            <v-btn
            color="info" block @click="signUp"
            :disabled="!valid1"
            >
            join now
            </v-btn>
            <v-alert
              :value="signupalert"
              type ="success"
              dense
              prominent
              dismissable
              transition="scale-transition"
              id = "signupalert"
            >
            Success. Account created, please log in.
            </v-alert>
            <v-alert
              :value="signupalertbad"
              type ="error"
              dense
              prominent
              dismissable
              transition="scale-transition"
              id = "signupalertbad"
            >
            Error creating account.
            </v-alert>
        </v-form>
        </v-card>
      </v-col>
    </v-row>
    </v-container>
    </v-app>
</template>


<script>
  import axios from 'axios';
  const apiClient = axios.create({ baseUrl: 'http://localhost:3000', headers: { 'Accept': 'application/json', 'Content-Type': 'application/json' } });
  export default {
    data(){
        return{
        valid: true,
        valid1: true,
        emailbox: '',
        emailbox1:'',
        passwordbox:'',
        passwordbox1:'',
        namebox:'',
        pass: String,
        pass1: String,
        signupalert:false,
        loginalert:false,
        signupalertbad:false,
        loginalertbad:false,
        emailRules: [
          v => !!v || 'E-mail is required',
          v => /.+@.+\..+/.test(v) || 'E-mail must be valid',
        ],
        passwordRules:[
            v => !!v || 'Password is required',
            v => (v && v.length >6) || 'Password must be more than 6 characters',
        ],
        nameRules: [
          v => !!v || 'Name is required',
          v => v.length <= 10 || 'Name must be less than 10 characters',
        ],
        checkbox: false,
      }
      },
      methods:{
            loginUser(){
                apiClient.post('/api/login', {
                    email: this.emailbox,
                    password: this.passwordbox
                })
                .then(response => {
                  console.log(response.data);
                  if(response.data == "success"){
                      this.loginalert = true;
                      this.loginalertbad = false;
                      window.location.replace('http://localhost:3000/discover');
                 }else{
                     console.log(response.data);
                     this.loginalertbad = true;
                     this.loginalert = false;
                    //  this.emailbox = '';
                    //  this.passwordbox = '';
                     this.$refs.form.reset();
                 }  
                })
                .catch(err => console.log(err));
            },
            signUp(){
             apiClient.post('/api/signup', {
                 name: this.namebox,
                 email: this.emailbox1,
                 password: this.passwordbox1
             })
             .then(response => {
                 if(response.data == "success"){
                      this.signupalert = true;
                      this.signupalertbad = false;
                      this.namebox = '';
                      this.emailbox1 = '';
                      this.passwordbox1 = '';
                 }else{
                     console.log(response.data);
                     this.signupalertbad = true;
                     this.signupalert = false;
                    //  this.namebox = '';
                    //  this.emailbox1 = '';
                    //  this.passwordbox1 = '';
                     this.$refs.form.reset();
                 } 
             })
             .catch(err => next(err));
         }
      }
  }
</script>

<style scoped>
#signupalert, #loginalert, #signupalertbad, #loginalertbad {
    margin-top:5px;
}
</style>