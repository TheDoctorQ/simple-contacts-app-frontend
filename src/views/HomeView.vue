<script>
import axios from 'axios';
export default {
  data: function () {
    return {
      message: "Welcome to Vue.js!",
      contacts: [],
      newContact: {
        first_name: "",
        last_name: "",
        email: "",
        phone_number: "",
        image: "",
      },
      currentContact: {},
      errors: []
    };
  },
  created: function () {
    this.indexContacts();
  },
  methods: {
    indexContacts: function () {
      console.log('index Contacts...')
      axios.get('/contacts.json').then(response => {
        console.log(response.data);
        this.contacts = response.data;
      });
    },
    showContact: function (contact) {
      console.log('show')
      // open the modal
      document.querySelector("#contact-details").showModal();
      // set the data for the modal
      this.currentContact = contact;
    },
    createContact: function () {
      console.log('creating ');
      axios.post('/contacts.json', this.newContact).then(response => {
        console.log(response.data);
        this.contacts.push(response.data)
        this.errors = [];
      }).catch(error => {
        console.log('something bad happened')
        this.errors = error.response.data.errors;
      })
    },
    updateContact: function (contact) {
      console.log('updating contact...')
      axios.patch(`/contacts/${this.currentContact.id}.json`, contact).then(response => {
        console.log(response.data);
      })
    },
    destroyContact: function (contact) {
      console.log('destroying contact')
      console.log(contact)
      axios.delete(`/contacts/${contact.id}`).then(response => {
        console.log(response.data)
        // find the proper index
        var index = this.contacts.indexOf(contact);
        // remove based on that index
        this.contacts.splice(index, 1)
      })
    }
  },
};
</script>

<template>
  <div class="home">
    <!-- contacts.each do |contact| -->
    <small v-for="error in errors">
      {{ error }}
      <br />
    </small>

    <p>
      First Name
      <input type="text" v-model="newContact.first_name" />
    </p>
    <p>
      Last Name
      <input type="text" v-model="newContact.last_name" />
    </p>
    <p>
      email
      <input type="text" v-model="newContact.email" />
    </p>
    <p>
      phone number
      <input type="text" v-model="newContact.phone_number" />
    </p>
    <p>
      image
      <input type="text" v-model="newContact.image" />
    </p>
    <button v-on:click="createContact">Make new contact</button>
    <div v-for="contact in contacts">
      {{ contact.first_name }}
      <br />
      <img v-bind:src="contact.image" />
      <br />
      <button v-on:click="showContact(contact)">More Info</button>
      <hr />
    </div>
    <dialog id="contact-details">
      <form method="dialog">
        <p>
          <b>first_name:</b>
          <input type="text" v-model="currentContact.first_name" />
        </p>
        <p>
          <b>last_name:</b>
          <input type="text" v-model="currentContact.last_name" />
        </p>
        <p>
          <b>email:</b>
          <input type="text" v-model="currentContact.email" />
        </p>
        <p>
          <b>phone_number:</b>
          <input type="text" v-model="currentContact.phone_number" />
        </p>
        <p>
          <b>image:</b>
          <input type="text" v-model="currentContact.image" />
        </p>
        <button v-on:click="updateContact(currentContact)">Update Contact</button>
        <button v-on:click="destroyContact(currentContact)">Remove Contact</button>

        <button>Close</button>
      </form>
    </dialog>
    <!-- <h1>{{ contacts }}</h1> -->
  </div>
</template>

<style>
img {
  width: 150px;
}
</style>