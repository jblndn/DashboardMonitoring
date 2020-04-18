<template>
    <div>
        <Navbar class="overflow-hidden" />

        <div class="container">

            <form>
                <v-text-field
                        v-model="name"
                        :error-messages="nameErrors"
                        label="Name"
                        required
                        @input="$v.name.$touch()"
                        @blur="$v.name.$touch()"
                ></v-text-field>
                <v-text-field
                        v-model="description"
                        label="Description"
                ></v-text-field>
                <v-text-field
                        v-model="email"
                        :error-messages="emailErrors"
                        label="E-mail"
                        @input="$v.email.$touch()"
                        @blur="$v.email.$touch()"
                ></v-text-field>
                <v-text-field
                        v-model="contactName"
                        :error-messages="contactNameErrors"
                        label="Contact name"
                        @input="$v.contactName.$touch()"
                        @blur="$v.contactName.$touch()"
                ></v-text-field>
                <v-text-field
                        v-model="phone"
                        :error-messages="phoneErrors"
                        label="Phone number"
                        @input="$v.phone.$touch()"
                        @blur="$v.phone.$touch()"
                ></v-text-field>
                <v-select
                        v-model="select"
                        :items="clientsName"
                        :error-messages="clientsErrors"
                        label="Client"
                        required
                        @change="$v.select.$touch()"
                        @blur="$v.select.$touch()"
                ></v-select>

                <v-btn class="mr-4" @click="submit">submit</v-btn>
                <v-btn @click="clear">clear</v-btn>
            </form>
            <!--<notifications group="add" />-->
        </div>
    </div>
</template>

<script>
    import Navbar from '~/components/Navbar.vue'
    import { validationMixin } from 'vuelidate'
    import { required, email, integer, alpha } from 'vuelidate/lib/validators'
    import db from '~/plugins/firebase'

    export default {
        components: {
            Navbar
        },
        mixins: [validationMixin],
        validations: {
            name: { required },
            email: { required, email },
            contactName: { required },
            phone: { integer },
            client: { required },
            description: {}
        },
        data: () => ({
            name: '',
            email: '',
            contactName: '',
            phone: '',
            description: '',
            select: null,
            clientsName: [],
            clientsId: []
        }),
        computed: {
            nameErrors () {
                const errors = []
                if (!this.$v.name.$dirty) return errors
                !this.$v.name.required && errors.push('Name is required.')
                return errors
            },
            emailErrors () {
                const errors = []
                if (!this.$v.email.$dirty) return errors
                !this.$v.email.email && errors.push('Must be valid e-mail')
                !this.$v.email.required && errors.push('E-mail is required')
                return errors
            },
            contactNameErrors () {
                const errors = []
                if (!this.$v.contactName.$dirty) return errors
                !this.$v.contactName.required && errors.push('Contact name is required')
                return errors
            },
            phoneErrors () {
                const errors = []
                if (!this.$v.phone.$dirty) return errors
                !this.$v.phone.integer && errors.push('Must be valid phone number')
                return errors
            },
            clientsErrors (){
                const errors = []
                if (!this.$v.client.$dirty) return errors
                !this.$v.client.required && errors.push('Contact name is required')
                return errors
            }
        },
        beforeMount(){
            this.selectClients()
        },
        methods: {
            selectClients(){
                let clients = db.ref('clients');
                let keys = [];
                let names = [];

                clients.on('value', function(snapshot) {
                    snapshot.forEach(function(childSnapshot) {
                        let childKey = childSnapshot.key;
                        let childDataName = childSnapshot.val().name;

                       keys.push(childKey);
                       names.push(childDataName);
                    });
                });

                this.clientsId = keys;
                this.clientsName = names;

            },
            submit () {
                let data = {};
                let projectId;

                //Format data
                data.name = this.name;
                data.email = this.email;
                data.contactName= this.contactName;
                data.phone = this.phone;
                projectId = this.formatId(this.name);

                //Send db
                db.ref( 'projects/' + projectId ).set({
                    name: data.name,
                    contactName:  data.contactName,
                    email: data.email,
                    tel: data.phone
                });


                //Reset form
                this.$v.$touch()
                this.$v.$reset()
                this.name = ''
                this.email = ''
                this.contactName = ''
                this.phone = ''

                //Notification send
                // this.$notify({
                //     group: 'add',
                //     title: 'Client ajouté',
                //     type: 'sucess',
                //     position: 'top right'
                // });

            },
            formatId (str){
                str = str.toLowerCase();

                str = str.replace(/[àáâãäå]/,"a");
                str = str.replace(/[éèëê]/,"e");
                str = str.replace(/[iíìïî]/,"i");
                str = str.replace(/[óòõöô]/,"o");
                str = str.replace(/[úùüû]/,"u");
                str = str.replace(/[ç]/,"c");
                str  = str.replace(/[^a-zA-Z0-9+]/g, "");


                return str
            },
            clear () {
                this.$v.$reset()
                this.name = ''
                this.email = ''
                this.contactName = ''
                this.phone = ''
            },
        },
    }
</script>

<style lang="scss">
    form{
        width: 40%;
        min-width: 500px;
        margin: 50px auto;

        .v-input{
            &.error--text{
                color: red;
                .v-text-field__details{
                    .v-messages__message{
                        color: red;
                    }
                }
            }
        }
    }

</style>