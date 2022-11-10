<template>
    <div v-on:click="deleteComment(); deleteMessageTitle()">
        <i class="material-symbols-outlined">
            delete
        </i>
    </div>
</template>
<script>
export default ({
    props: {
        id: '',
    },
    data() {
        return {
        }
    },
    methods: {
        deleteComment() {
            let url = String('http://localhost/api/comments/' + this.id)
            axios.delete(url, {})
                .then((response) => {
                    //console.log(response.status);
                    this.$emit('deleteMessageBody', { resp: response.status });
                    this.$emit('getData');
                })
                .catch((error) => {
                    console.log(error);
                });
        },
        deleteMessageTitle(){
            this.$emit('deleteMessageTitle', { id: this.id });
            this.$emit('showeMessageTitle');
        },
    }
})
</script>