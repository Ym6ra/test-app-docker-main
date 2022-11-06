<template>
    <div v-bind:class="[{ display: !creatCommentVal }, { hidden: creatCommentVal }]">
        <div class="popup">
            <div>
                <h2>Оставьте новый комментарий</h2>
            </div>
            <div>
                <div>Имя автора</div>
                <div>Текст комментария</div>
                <div>Дата написания</div>
            </div>
            <div>
                <div><input v-model="name"></div>
                <div><input v-model="text"></div>
                <div>
                    <date-picker v-model="date" valueType="format"></date-picker>
                </div>
            </div>
            <div v-on:click="newComment(); uploadComments()">
                <i class="material-symbols-outlined">
                    add
                </i>
            </div>
        </div>
    </div>
</template>

<script>

import DatePicker from 'vue2-datepicker';
import 'vue2-datepicker/index.css';

export default ({
    props: {
        creatCommentVal: true,
    },
    components: { DatePicker },
    data() {
        return {

            name: '',
            text: '',
            date: '',
        }
    },
    methods: {
        newComment(){
            this.$emit('newComment');
        },
        uploadComments() {
            let url = 'http://localhost/api/comments/';
            axios.post(url, {
                name: this.name,
                text: this.text,
                date: this.date,
            })
                .then((response) => {
                    //console.log(response.status);
                    this.$emit('getData');
                })
                .catch((error) => {
                    console.log(error);
                });
        },
    }
})
</script>
<!--this.comments-->