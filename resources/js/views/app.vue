<template>
    <div class="app__body">
        <div class="table__head">
            <div class="transform__var" v-on:click="showeSort(showeSortVal)">
                <i class="material-symbols-outlined">
                    sort
                </i>
            </div>
            <div class="transform__element hidden__transformed" v-bind:class="{ display__transformed: !showeSortVal }">
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
                <div class="element__part" v-on:click="getData">
                    <i class="material-symbols-outlined">
                        refresh
                    </i>
                </div>
            </div>
        </div>
        <div class="table__body" v-for="comment of paginatetComments">
            <commentElem v-bind:id="comment.id" v-bind:name="comment.name" v-bind:text="comment.text"
                v-bind:date="comment.date" v-bind:showeComment="showeCommentVal" />
            <div class="body__elem">
                <div v-on:click="showeInput(comment.id, comment.name, comment.text, comment.date, !showeInputVal)">
                    <i class="material-symbols-outlined">
                        edit_square
                    </i>
                </div>
                <div v-on:click="deleteComment(comment.id)">
                    <i class="material-symbols-outlined">
                        delete
                    </i>
                </div>
            </div>
        </div>
        <div class="pagination">
            <div class="page" :class="{ selected: page === pageNumber }" v-for="page in pages"
                v-on:click="pageClick(page)">
                {{ page }}
            </div>
        </div>
        <commentUpdate v-bind:id="this.id" v-bind:name="this.name" v-bind:text="this.text" v-bind:date="this.date"
            v-bind:showeInputVal="this.showeInputVal" @getData="getData" @showeInput="unshoweInput" />
        <commentCreate v-bind:creatCommentVal="this.creatCommentVal" @getData="getData"
            @newComment="newComment(!creatCommentVal)" />
    </div>
</template>
<script>
import DatePicker from 'vue2-datepicker';
import 'vue2-datepicker/index.css';
import commentCreate from './commentCreate.vue';
import commentUpdate from './commentUpdate.vue';
import commentElem from './commentElem.vue';

const default_layout = "default";


export default {
    components: {
        commentCreate,
        DatePicker,
        commentUpdate,
        commentElem,

    },
    computed: {
        pages() {
            let pagCount = [];
            for (let i = 1; i < Math.round((this.comments).length / this.commentsPerPage) + 1; i++) {
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
            boolId: true,
            boolDate: false,
            id: null,
            name: 'Name',
            text: 'Text',
            date: 'Date',
            newName: '',
            newText: '',
            newDate: '',
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
        deleteComment(a) {
            console.log(a);
            let url = String('http://localhost/api/comments/' + a)
            axios.delete(url, {})
                .then((response) => {
                    //console.log(response.status);
                })
                .catch((error) => {
                    console.log(error);
                });
            this.getData();
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
    }
};

</script>
<style>

</style>