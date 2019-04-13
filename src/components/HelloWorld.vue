<template>
  <v-container>
      <v-flex xs12 sm12 md12>
          <v-text-field
            v-model="sub"
            label="tfone"
            placeholder="Title"
            solo
          ></v-text-field>
      </v-flex>

      <v-flex xs12 sm12 md12>
        <v-textarea
          v-model="cont"
          solo
          name="input-7-4"
          label="Solo textarea"
          value=""
          placeholder="Content"
        ></v-textarea>
      </v-flex>
      <v-flex xs12 sm12 md12>

      <v-alert
        :value="errmsg"
        type="error"
        dismissible
      >
        Note not found
      </v-alert>
     
      </v-flex>
      <v-flex xs12 sm12 md12>
        <div class="footer">
          <v-btn color="warning" 
          v-on:click="del" 
          :disabled="((typeof this.sub === 'undefined'|| this.sub === ''))">
          Delete
          </v-btn>
          <v-btn color="success" 
          :disabled="((typeof this.sub === 'undefined'|| this.sub === '') || (typeof this.cont === 'undefined'|| this.cont === ''))"
          v-on:click="save"
          >Save</v-btn>
          <v-btn color="info" 
          v-on:click="view"
          :disabled="((typeof this.sub === 'undefined'|| this.sub === ''))"
          >View</v-btn>
        </div>
      </v-flex>

  </v-container>
</template>

<script>
  export default {
    data: () => {
      return({
        sub: "",
        cont: "",
        errmsg: false
      })
    },  
    methods: {
      save: function(event) {
        if (this.validate()) {
          let subject = this.sub;
          if (/\s/g.test(subject)) {
            subject = subject.split(/[ ,]+/).join('_')
          }
          console.log({
              "Subject" : subject,
              "Content" : this.cont
          })
          this.axios.post('https://netpad-api.herokuapp.com/api/postNote', {
            "Subject" : subject,
            "Content" : this.cont
          }).then((response) => {
            console.log(response)
          })
        }

      }, 
      del: function(event) {
        if (this.validate()) {
          let subject = this.sub;
          if (/\s/g.test(subject)) {
            subject = subject.split(/[ ,]+/).join('_')
          }
          console.log({
              "Subject" : subject,
              "Content" : this.cont
          })
          this.axios.delete('https://netpad-api.herokuapp.com/api/getNote/'+subject)
          .then((response) => {
            console.log(response)
            if (response.data.operation === "successful") {
              this.sub = '';
              this.cont = '';
            } else {
              this.errmsg = true;
            }
          })
        }

      }, 
      view: function(event) {
        if (this.validate()) {
          let subject = this.sub
          if (/\s/g.test(subject)) {
            subject = subject.split(/[ ,]+/).join('_')
          }
          console.log({
              "Subject" : subject,
              "Content" : this.cont
          })
          this.axios.get('https://netpad-api.herokuapp.com/api/getNote/'+subject)
          .then((response) => {
            console.log(response)
            if (response.data.operation == "note not found") {
              this.errmsg = true;
            }
            else {
              this.cont = response.data.Content;
            }
          })
        }

      }, 
      validate: function() {
        if ((typeof this.sub === 'undefined'|| this.sub === '')) {
          return false;
        }
        return true;
      }
    }
  }
</script>

<style>
.footer {
  position: fixed;
  left: 0;
  bottom: 0;
  width: 100%;
  color: white;
  text-align: center;
}
</style>
