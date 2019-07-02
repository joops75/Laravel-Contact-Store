<template>
    <div>
        <h1>Create/Edit Contacts</h1>
        <form action="#" @submit.prevent="edit ? updateContact(contact.id) : createContact()">
            <div class="form-group">
                <label>Name</label>
                <input v-model="contact.name" type="text" name="name" class="form-control">
            </div>
            <div class="form-group">
                <label>Email</label>
                <input v-model="contact.email" type="email" name="email" class="form-control">
            </div>
            <div class="form-group">
                <label>Phone</label>
                <input v-model="contact.phone" type="text" name="phone" class="form-control">
            </div>
            <div class="form-group">
                <button v-show="!edit" type="submit" class="btn btn-primary">Add Contact</button>
                <button v-show="edit" type="submit" class="btn btn-primary">Update Contact</button>
            </div>
        </form>
        <ul class="list-group">
            <li class="list-group-item" v-for="contact in list">
                <strong>{{contact.name}}</strong> {{contact.email}} {{contact.phone}}
                <button @click="showContact(contact.id)" class="btn btn-secondary btn-sm">Edit</button>
                <button @click="deleteContact(contact.id)" class="btn btn-danger btn-sm">Delete</button>
            </li>
        </ul>
    </div>
</template>

<script>
    export default {
        data() {
            return {
                edit: false,
                list: [],
                contact: {
                    id: '',
                    name: '',
                    email: '',
                    phone: ''
                }
            };
        },
        mounted() {
            console.log('mounted');
            this.fetchContactList();
        },
        methods: {
            fetchContactList() {
            console.log('fetching contact list');
            axios.get('api/contacts')
                .then(res => {
                    console.log(res.data);
                    this.list = res.data;
                })
                .catch(err => {
                    console.log(err);
                })
            },
            createContact() {
                console.log('creating contact');
                const params = Object.assign({}, this.contact);
                axios.post('api/contact/store', params)
                    .then(() => {
                        this.contact.name = '';
                        this.contact.email = '';
                        this.contact.phone = '';
                        this.edit = false;
                        this.fetchContactList();
                    })
                    .catch(err => {
                        console.log(err);
                    });
            },
            showContact(id){
                axios.get('/api/contact/' + id)
                    .then(({ data }) => {
                        this.contact.id = data.id;
                        this.contact.name = data.name;
                        this.contact.email = data.email;
                        this.contact.phone = data.phone;
                    })
                    .catch(err => {
                        console.log(err);
                    });
                this.edit = true;
            },
            updateContact(id) {
                console.log('updating contact ' + id);
                const params = Object.assign({}, this.contact);
                axios.patch('/api/contact/' + id, params)
                    .then(() => {
                        this.contact.name = '';
                        this.contact.email = '';
                        this.contact.phone = '';
                        this.edit = false;
                        this.fetchContactList();
                    })
                    .catch(err => {
                        console.log(err);
                    });
            },
            deleteContact(id) {
                axios.delete('api/contact/' + id)
                    .then(() => {
                        this.fetchContactList();
                    })
                    .catch(err => {
                        console.log(err);
                    });
            }
        }
    }
</script>
