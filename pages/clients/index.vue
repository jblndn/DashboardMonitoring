<template>
    <div>
        <Navbar class="overflow-hidden" />
        <div class="container">
            <v-layout row wrap justify-flex-start>
                <v-card
                        v-for="client in clients" :key="client.clientId"
                >
                    <v-card-text>
                        <p class="display-1 text--primary">
                            {{ client.name }}
                        </p>
                        <div class="text--primary">
                            Contact : {{ client.contactName }}
                        </div>
                        <div class="text--primary">
                            Email : <a :href="'mailto:' + client.email">{{ client.email }}</a>
                        </div>
                        <div class="text--primary">
                            Phone : {{ client.phone }}
                        </div>
                    </v-card-text>
                    <v-card-actions>
                        <v-btn
                                text
                                color="deep-purple accent-4"
                        >
                            Learn More
                        </v-btn>
                    </v-card-actions>
                </v-card>

            </v-layout>
        </div>
    </div>
</template>

<script>
    import Navbar from '~/components/Navbar.vue'
    import db from '~/plugins/firebase'

    export default {
        components: {
            Navbar
        },
        data: () => ({
            clients: []
        }),
        beforeMount(){
            let clients = db.ref('clients');
            let data = [];

            clients.on('value', function(snapshot) {
                snapshot.forEach(function(childSnapshot) {
                    let childData = childSnapshot.val();

                    let childKey = childSnapshot.key;
                    childData.clientId = childKey;

                    data.push(childData);

                });
            });

            this.clients = data;
        }
    }
</script>

<style lang="scss">
    .v-card{
        display: inline-block;
        padding: 20px;
        min-width: 400px;
        max-width: 550px;
        margin-right: 50px;
    }
</style>

