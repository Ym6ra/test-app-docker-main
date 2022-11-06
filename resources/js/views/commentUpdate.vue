<template>
    <div class="popup" v-bind:class="[{ display: !showeInputVal }, { hidden: showeInputVal }]">
        <div class="popup__elem">
            <div>{{ this.id }}</div>
        </div>
        <div class="popup__elem">
            <div>{{ this.name }}</div>
            <input v-model="newName">
        </div>
        <div class="popup__elem">
            <div>{{ this.text }}</div>
            <input v-model="newText">
        </div>
        <div class="popup__elem">
            <div>{{ this.date }}</div>
            <date-picker v-model="newDate" valueType="format"></date-picker>
        </div>
        <div class="popup__elem" v-on:click="showeInput(); patchComment()">
            <i class="material-symbols-outlined">
                ios_share
            </i>
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
        //myMacros(){
        //    console.log('macro');
        //    this.showeInput;
        //    this.patchComment;
        //},
        showeInput(){
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
                    this.$emit('getData');
                })
                .catch((error) => {
                    console.log(error);
                });
        },
    }
}
</script>