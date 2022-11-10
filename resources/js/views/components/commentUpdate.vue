<template>
    <div class="popup" v-bind:class="[{ display: !showeInputVal }, { hidden: showeInputVal }]">
        <div class="popup__info">
            <div class="info__title">
                <h3>Редактировать комментарий №{{ this.id }}</h3>
                <div class="info__button" v-on:click="showeInput();">
                    <i class="material-symbols-outlined">
                        close
                    </i>
                </div>
            </div>
            <div class="info__elem">
                <div class="elem__part">
                    <div>{{ this.name }}</div>
                    <input v-model="newName">
                </div>
                <div class="elem__part">
                    <div>{{ this.date }}</div>
                    <date-picker v-model="newDate" valueType="format"></date-picker>
                </div>
            </div>
            <div class="info__body">
                <div>{{ this.text }}</div>
                <textarea v-model="newText" placeholder="Ваш комментарий"></textarea>
            </div>
            <div class="info__button" v-on:click="showeInput(); patchComment(); updateMessageTitle()">
                <i class="material-symbols-outlined">
                    ios_share
                </i>
            </div>
        </div>
    </div>
</template>

<script>
import DatePicker from 'vue2-datepicker';
import 'vue2-datepicker/index.css';

export default {
    components: {
        DatePicker,
    },
    props: {
        id: '',
        name: '',
        text: '',
        date: '',
        showeInputVal: true,
    },
    data() {
        return {
            newName: '',
            newText: '',
            newDate: '',
        }
    },
    methods: {
        showeInput() {
            this.$emit('showeInput')
        },
        patchComment() {
            let url = String('http://localhost/api/comments/' + this.id)
            axios.patch(url, {
                name: this.newName,
                text: this.newText,
                date: this.newDate,
            })
                .then((response) => {
                    //console.log(response.status);
                    this.$emit('updateMessageBody', { resp: response.status });
                    this.$emit('getData');
                })
                .catch((error) => {
                    console.log(error);
                });
        },
        updateMessageTitle() {
            this.$emit('updateMessageTitle', { id: this.id });
            this.$emit('showeMessageTitle');
        }
    }
}
</script>