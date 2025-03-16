<template>
  <div id="app">
    <h1>Contacts</h1>
    
    <!-- NEW CONTACT -->
    <div>
      <input v-model="newContact.name" placeholder="First Name & Last Name" />
      <input v-model="newPhoneNumber" 
             placeholder="Phone number" 
             @keyup.enter="addContact" 
             @input="validatePhoneNumber" />
      <button @click="addContact">Add</button>
    </div>
    
    <!-- CONTACT LIST -->
    <div class="contact-list">
      <div v-for="(contact) in contacts" :key="contact.id" class="contact-card">
        <div v-if="!contact.isEditing">
          <h2 @dblclick="editContact(contact)">{{ contact.name }}</h2>
          <ul>
            <li v-for="(phone, idx) in contact.phoneNumbers" :key="idx">{{ phone }}</li>
          </ul>
          <button @click="editContact(contact)">Edit</button>
          <button @click="removeContact(contact)">Delete</button>
        </div>
        
        <!-- EDIT CONTACT -->
        <div v-if="contact.isEditing">
          <input v-model="contact.name" @keyup.enter="saveContact(contact)" @keyup.esc="cancelEdit(contact)" />
          <div v-for="(phone, idx) in contact.phoneNumbers" :key="idx">
            <input v-model="contact.phoneNumbers[idx]" @keyup.enter="saveContact(contact)" @keyup.esc="cancelEdit(contact)" />
            <button @click="removePhoneNumber(contact, idx)">Delete number</button>
          </div>
         
          <button @click="saveContact(contact)">Save</button>
          <button @click="cancelEdit(contact)">Cancel</button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      newContact: {
        name: '',
        phoneNumbers: []
      },
      newPhoneNumber: '',
      contacts: [],
      originalData: null
    };
  },
  methods: {
    validatePhoneNumber(event) {
      const validChars = /^[0-9+]+$/;
      if (!validChars.test(event.target.value)) {
        this.newPhoneNumber = this.newPhoneNumber.slice(0, -1);
      }
    },

   addPhoneNumber() {
  if (this.newPhoneNumber.trim()) {
    if (this.newContact.name) {
      this.newContact.phoneNumbers.push(this.newPhoneNumber.trim());
    }
    this.newPhoneNumber = '';
  }
},

    addContact() {
  this.addPhoneNumber(); 
  if (this.newContact.name.trim() && this.newContact.phoneNumbers.length) {
    this.contacts.push({
      id: Date.now(),
      name: this.newContact.name,
      phoneNumbers: [...this.newContact.phoneNumbers],
      isEditing: false
    });
    this.newContact.name = '';
    this.newContact.phoneNumbers = [];
    this.newPhoneNumber = ''; 
  }
},

    editContact(contact) {
      contact.isEditing = true;
      this.originalData = { ...contact, phoneNumbers: [...contact.phoneNumbers] };
    },

    saveContact(contact) {
      contact.isEditing = false;
    },

    cancelEdit(contact) {
      Object.assign(contact, this.originalData);
      contact.isEditing = false;
    },

    removeContact(contact) {
      this.contacts = this.contacts.filter(c => c !== contact);
    },

    addPhoneNumberToEdit(contact) {
      if (this.newPhoneNumber.trim()) {
        contact.phoneNumbers.push(this.newPhoneNumber.trim());
        this.newPhoneNumber = '';
      }
    },

    removePhoneNumber(contact, index) {
      contact.phoneNumbers.splice(index, 1);
    }
  }
};
</script>

<style>
body{
  font-family: 'Poppins', sans-serif;
  background-color: #f0f4f8;
  color: #333;
  display: flex;
  justify-content: center;
  align-items: center;
  margin: 10px;
}
.contact-list {
  display: flex;
  flex-wrap: wrap;
}

.contact-card {
  width: 300px;
  padding: 20px;
  margin: 10px;
  border: 1px solid #ccc;
  border-radius: 10px;
  background-color: #f9f9f9;
}

input {
  width: 100%;
  padding: 10px 15px;
  margin-bottom: 10px;
  border: 1px solid #ccc;
  border-radius: 8px;
  outline: none;
  transition: all 0.2s ease-in-out;
}

button {
  margin: 5px;
  border-radius: 5px;
  background-color: #007bff;
  border: none;
  padding: 10px 20px;
  border-radius: 8px;
  cursor: pointer;
  transition: background-color 0.2s ease-in-out;
  }
.contact-card button {
  margin-right: 10px;
  background-color: #ff4c4c;
}
</style>
