<template>
    <div v-bind:class="[{ display: !creatCommentVal }, { hidden: creatCommentVal }]">
        <div class="popup">
            <div class="popup__info">
                <div class="info__title">
                    <h3>Оставьте новый комментарий</h3>
                    <div class="info__button" v-on:click="newComment();">
                        <i class="material-symbols-outlined">
                            close
                        </i>
                    </div>
                </div>
                <div class="info__elem">
                    <div class="elem__part">
                        <span>Имя автора</span>
                        <input v-model="name">
                    </div>
                    <div class="elem__part">
                        <span>Дата написания</span>
                        <date-picker v-model="date" valueType="format"></date-picker>
                    </div>
                </div>
                <div class="info__body">
                    <span>Текст комментария</span>
                    <textarea v-model="text" placeholder="Ваш комментарий"></textarea>
                </div>
                <div class="info__button" v-on:click="newComment(); uploadComments()">
                    <i class="material-symbols-outlined">
                        ios_share
                    </i>
                </div>
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
        newComment() {
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