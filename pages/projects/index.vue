<template>
    <div>
        <Navbar class="overflow-hidden" />
        <div class="container">
            <v-layout row wrap justify-flex-start>
                <v-card
                        v-for="project in projects" :key="project.projectId"
                >
                    <v-card-text>
                        <p class="display-1 text--primary">
                            {{ project.name }}
                        </p>
                        <div class="text--primary">
                            Contact : {{ project.contactName }}
                        </div>
                        <div class="text--primary">
                            Email : <a :href="'mailto:' + project.email">{{ project.email }}</a>
                        </div>
                        <div class="text--primary">
                            Phone : {{ project.phone }}
                        </div>
                    </v-card-text>
                    <v-card-actions>
                        <v-icon color="blue darken-2" @click="deleteClient(project.projectId)">mdi-delete</v-icon>
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
            projects: [],
        }),
        beforeMount(){
            let projects = db.ref('projects');
            let data = [];

            projects.on('value', function(snapshot) {
                snapshot.forEach(function(childSnapshot) {
                    let childData = childSnapshot.val();

                    let childKey = childSnapshot.key;
                    childData.projectId = childKey;

                    data.push(childData);

                });
            });

            this.projects = data;
        },
        methods: {
            deleteClient(id){
                let project = db.ref('projects/' + id );

                for (let i = 0; i < this.projects.length; i++) {
                    if (this.projects[i].projectId == id) this.projects.splice(i, 1);
                }

                project.remove();

            }
        }
    }
</script>

<style lang="scss">
    .v-card{
        display: inline-block;
        padding: 20px;
        min-width: 300px;
        max-width: 550px;
        margin-right: 50px;
        margin-bottom: 50px;

        .v-card__actions{
            justify-content: flex-end;
        }
    }
</style>

