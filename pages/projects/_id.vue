<template>
    <div class="container">
        <h1 class="display-3">
            {{ name }}
        </h1>
        <div class="container-content">
            <div class="container-status title">
                Status : <span :class="status"></span>{{ status}}
            </div>
            <h2 class="container-client headline">
                Client : {{ client }}
            </h2>
            <div class="title">
                Contact : {{ contactName }}
            </div>
            <div class="title">
                Email : <a :href="'mailto:' + email">{{ email }}</a>
            </div>
            <div v-if="phone" class="phone">
                Phone : {{ phone }}
            </div>
            <div v-if="description" class="title">
                Description : {{ description }}
            </div>
        </div>

    </div>
</template>

<script>
    import db from '~/plugins/firebase'

    export default {
        data: () => ({
            name: '',
            email: '',
            contactName: '',
            phone: '',
            status: '',
            description: '',
            client: ''
        }),
        beforeMount(){
            this.getProject();
        },
        methods: {
            getProject(){
                let project = db.ref('projects/' + this.$route.params.id);
                let data = [];

                project.on('value', function(snapshot) {
                    let childData = snapshot.val();
                    data.name = childData.name;
                    data.email = childData.email;
                    data.contactName = childData.contactName;
                    data.phone = childData.phone;
                    data.description = childData.description;
                    data.status = childData.status;
                    data.client = childData.clientId;
                });

                this.name = data.name;
                this.email = data.email;
                this.contactName = data.contactName;
                this.phone = data.phone;
                this.description = data.description;
                this.client = data.client;
                data.status = data.status.toLowerCase();
                data.status = data.status.replace(/ /g, "-");
                this.status = data.status;
            }
        }
    }
</script>

<style lang="scss">
    .container-content{
        padding-left: 20px;
        .container-status{
            margin: 20px 0;
            .beginning, .waiting, .in-progress, .done{
                display: inline-block;
                margin: 0 10px;
                border-radius: 50%;
                width: 10px;
                height: 10px;
            }
            .beginning{
                background-color: #607D8B;
            }
            .waiting{
                background-color: #FF9800;
            }
            .in-progress{
                background-color: #FFEB3B;
            }
            .done{
                background-color: #4CAF50;
            }
        }
        .container-client{
            margin: 20px 0;
        }
    }
</style>