<template>
    <div class="app__body">
        <div class="table">
            <div class="table__head">
                <div class="transform__var" v-on:click="showeSort(showeSortVal)">
                    <i class="material-symbols-outlined">
                        sort
                    </i>
                </div>
                <div class="transform__element hidden__transformed"
                    v-bind:class="{ display__transformed: !showeSortVal }">
                    <div class="element__part" v-on:click="sortById(boolId)">
                        <div>Сортировка по номеру</div>
                        <i class="material-symbols-outlined">
                            unfold_more
                        </i>
                    </div>
                    <div class="element__part" v-on:click="sortByDate(boolDate)">
                        <div>Сортировка по дате</div>
                        <i class="material-symbols-outlined">
                            unfold_more
                        </i>
                    </div>
                </div>
                <div class="head__element">
                    <div class="element__part" v-on:click="newComment(!creatCommentVal)">
                        <i class="material-symbols-outlined">
                            add_comment
                        </i>
                    </div>
                </div>
            </div>
            <div class="table__body" v-for="comment of paginatetComments">
                <div class="body__elem buttons">
                    <div v-on:click="showeInput(comment.id, comment.name, comment.text, comment.date, !showeInputVal)">
                        <i class="material-symbols-outlined">
                            edit_square
                        </i>
                    </div>
                    <commentDelete v-bind:id="comment.id" @getData="getData"
                        @deleteMessageTitle="choseDeleteTitle"
                        @deleteMessageBody="choseDeleteBody" />
                    <!--<div v-on:click="deleteComment(comment.id); deletMessageTitle()">
                        <i class="material-symbols-outlined">
                            delete
                        </i>
                    </div>-->
                </div>
                <hr>
                <commentElem v-bind:id="comment.id" v-bind:name="comment.name" v-bind:text="comment.text"
                    v-bind:date="comment.date" v-bind:showeComment="showeCommentVal" />
            </div>
        </div>
        <nav aria-label="Page navigation">
            <ul class="pagination">
                <li class="page-item" v-for="page in pages" v-on:click="pageClick(page)">
                    <a class="page-link" :class="{ selected: page === pageNumber }">{{ page }}</a>
                </li>
            </ul>
        </nav>
        <div class="popup popupMessage" v-bind:class="[{ display: !showeMessageVal }, { hidden: showeMessageVal }]">
            <div class="popup__info">
                <div class="info__title">
                    <h3>{{ messageTitle }}</h3>
                    <div class="info__button" v-on:click="hiddenMessage()">
                        <i class="material-symbols-outlined">
                            close
                        </i>
                    </div>
                </div>
            </div>
            <div class="info__body">
                {{ messageBody }}
            </div>
        </div>
        <commentUpdate 
        v-bind:id="this.id" 
        v-bind:name="this.name" 
        v-bind:text="this.text" 
        v-bind:date="this.date"
        v-bind:showeInputVal="this.showeInputVal" 
        @getData="getData" 
        @showeInput="unshoweInput"
        @updateMessageTitle="choseUpdateTitle" 
        @updateMessageBody="choseUpdateBody" />
        <commentCreate 
        v-bind:creatCommentVal="this.creatCommentVal" 
        @getData="getData"
        @newComment="newComment(!creatCommentVal)" 
        @createMessageTitle="choseCreateTitle"
        @createMessageBody="choseCreateBody" />
    </div>
</template>
<script>
import DatePicker from 'vue2-datepicker';
import 'vue2-datepicker/index.css';
import commentCreate from './components/commentCreate.vue';
import commentDelete from './components/commentDelete.vue';
import commentUpdate from './components/commentUpdate.vue';
import commentElem from './components/commentElem.vue';


const default_layout = "default";


export default {
    components: {
        commentCreate,
        DatePicker,
        commentUpdate,
        commentElem,
        commentDelete,

    },
    computed: {
        pages() {
            let pagCount = [];
            for (let i = 1; i < Math.ceil((this.comments).length / this.commentsPerPage) + 1; i++) {
                pagCount.push(i);
            }
            return pagCount;
        },
        paginatetComments() {
            let from = (this.pageNumber - 1) * this.commentsPerPage;
            let to = from + this.commentsPerPage;
            return Object.values(this.comments).slice(from, to);
        },
    },
    data() {
        return {
            pageNumber: 1,
            commentsPerPage: 3,
            showeSortVal: true,
            showeInputVal: true,
            creatCommentVal: true,
            showeCommentVal: false,
            showeMessageVal: true,
            messageTitleVar: '',
            messageBodyVar: '',
            boolId: true,
            boolDate: false,
            id: null,
            messageTitle: '',
            messageBody: '',
            name: 'Name',
            text: 'Text',
            date: 'Date',
            newName: '',
            newText: '',
            newDate: '',
            deleteId: '',
            comments: {},
            commentsArray: [],
            filters: {},
        }
    },
    mounted() {
        this.getData()
    },
    methods: {
        getData() {

            axios.get('http://localhost/api/comments/', {
                params: this.filters
            }).then(response => {
                this.comments = Object.values(response.data);
            })
        },
        sortById(bo) {
            if (bo == true) {
                this.boolId = false;
                this.comments.sort(function (a, b) {
                    return a.id - b.id;
                })
            } else {
                this.boolId = true;
                this.comments.sort(function (a, b) {
                    return b.id - a.id;
                })
            }
            return this.boolId;
        },
        sortByDate(bo) {
            if (bo == true) {
                this.boolDate = false;
                this.comments.sort(function (a, b) {
                    return a.date.localeCompare(b.date);
                })
            } else {
                this.boolDate = true;
                this.comments.sort(function (a, b) {
                    return b.date.localeCompare(a.date);
                })

            }
            return this.boolDate;
        },
        showeSort(comBool) {
            this.showeSortVal = !comBool;
        },
        newComment(comBool) {
            this.creatCommentVal = comBool;
        },
        unshoweInput() {
            this.showeInputVal = !this.showeInputVal;
        },
        showeInput(comId, comName, comText, comDate, comBool) {
            this.id = comId;
            this.name = comName;
            this.text = comText;
            this.date = comDate;
            this.showeInputVal = comBool;
        },
        pageClick(page) {
            this.pageNumber = page;
        },
        choseCreateTitle(){
            let data = {id: ''}
            let val = 'create';
            this.showeMessageTitle(val, data);
        },
        choseUpdateTitle(data) { 
            let val = 'update';
            this.showeMessageTitle(val,data);
        },
        choseDeleteTitle(data) { 
            let val = 'delete';
            this.showeMessageTitle(val,data);
        },
        choseCreateBody(data) {
            let val = 'create';
            this.showeMessageBody(val,data);
        },
        choseUpdateBody(data) {
            let val = 'update';
            this.showeMessageBody(val,data);
        },
        choseDeleteBody(data) {
            let val = 'delete';
            this.showeMessageBody(val,data);
        },
        showeMessageTitle(val,data) {
            if (val == 'delete') {
                this.messageTitle = `Удаление комментария №` + data.id;
            } else if (val == 'create') {
                this.messageTitle = `Создание нового комментария`;
            } else if (val == 'update') {
                this.messageTitle = `Изменение комментария № ` + data.id;
            } else {
                console.log(val);
            }
            this.showeMessageVal = false;
        },
        showeMessageBody(val, data) {
            if (val == 'delete') {
                if (data.resp == 200) {
                    this.messageBody = `Вы удалили комментарий! 
                                Изменения вступят в силу через несколько секунд. 
                                Спасибо за ожидание!`
                } else {
                    this.messageBody = `Комментарий не удален. 
                                Возникла непредвиденная ошибка. 
                                По пробуйте позднее!`
                }
            } else if (val == 'create') {
                if (data.resp == 200) {
                    this.messageBody = `Вы создали новый комментарий! 
                                Изменения вступят в силу через несколько секунд. 
                                Спасибо за ожидание!`
                } else {
                    this.messageBody = `Комментарий не создан. 
                                Возникла непредвиденная ошибка. 
                                По пробуйте позднее!`
                }
            } else if (val == 'update') {
                if (data.resp == 200) {
                    this.messageBody = `Вы изменили комментарий! 
                                Изменения вступят в силу через несколько секунд. 
                                Спасибо за ожидание!`
                } else {
                    this.messageBody = `Комментарий не изменен. 
                                Возникла непредвиденная ошибка. 
                                По пробуйте позднее!`
                }
            } else {
                console.log(val);
                console.log(data);
            }
        },
        hiddenMessage() {
            this.showeMessageVal = true;
            this.messageTitle = '';
            this.messageBody = '';
        },
    }
};

</script>
<style>

</style>