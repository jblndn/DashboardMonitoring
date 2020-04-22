<template>
    <div class="container">
        <v-layout row wrap justify-flex-start>
            <v-card
                    v-for="project in projects"
                    :key="project.projectId"
                    :class="project.status"
                    :to="'Projects/' + project.projectId"
                    link
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
                    <div v-if="project.phone" class="text--primary">
                        Phone : {{ project.phone }}
                    </div>
                </v-card-text>
                <v-card-actions>
                    <v-icon color="blue darken-2" @click="deleteProject(project.projectId)">mdi-delete</v-icon>
                </v-card-actions>
            </v-card>

        </v-layout>
    </div>

</template>

<script>
    import db from '~/plugins/firebase'

    export default {
        data: () => ({
            projects: [],
        }),
        beforeMount(){
            this.getProjects();
        },
        methods: {
            getProjects(){
                let projects = db.ref('projects');
                let data = [];

                projects.on('value', function(snapshot) {
                    snapshot.forEach(function(childSnapshot) {
                        let childData = childSnapshot.val();
                        childData.status = childData.status.toLowerCase();
                        childData.status = childData.status.replace(/ /g, "-");

                        let childKey = childSnapshot.key;
                        childData.projectId = childKey;

                        data.push(childData);

                    });
                });

                this.projects = data;

            },
            deleteProject(id){
                let project = db.ref('projects/' + id );
                project.remove();
                this.getProjects();
            }
        }
    }
</script>

<style lang="scss">
    .v-card{
        padding: 20px;
        width: 350px;
        margin-right: 50px;
        margin-bottom: 50px;
        display: flex;
        flex-direction: column;
        justify-content: space-between;
        text-decoration: none!important;

        &:hover{
            transform: scale(1.02);
            transition: transform 300ms ease-in-out;
        }

        &.beginning, &.waiting, &.in-progress, &.done{
            border-bottom-width: 2px;
            border-bottom-style: solid;
        }
        &.beginning{
            border-bottom-color: #607D8B;
        }
        &.waiting{
            border-bottom-color: #FF9800;
        }
        &.in-progress{
            border-bottom-color: #FFEB3B;
        }
        &.done{
            border-bottom-color: #4CAF50;
        }
        .v-card__text{
            text-align: left
        }
        .v-card__actions{
            justify-content: flex-end;
        }

    }
</style>

