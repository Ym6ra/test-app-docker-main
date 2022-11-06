<template>
    <div>
        <div class="table__head">
            <div class="transform__var" v-on:click="showeSort(showeSortVal)">
                <i class="material-symbols-outlined">
                    sort
                </i>
            </div>
            <div class="transform__element hiden__transformed" v-bind:class="{ display__transformed: !showeSortVal }">
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
            <div class="body__elem">
                <div class="elem__user">
                    <i class="material-symbols-outlined">
                        person
                    </i>
                    <span>
                        {{ comment.name }}
                    </span>
                </div>
                <div class="elem__id">
                    # {{ comment.id }}
                </div>
            </div>
            <div class="body__elem">
                <div class="elem__text">
                    <div>{{ comment.text }}</div>
                </div>
                <div class="elem__date">
                    <div>Дата публикации: {{ comment.date }}</div>
                </div>
            </div>
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
        <div class="popup" v-bind:class="[{ display: !showeInputVal }, { hiden: showeInputVal }]">
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
            <div class="popup__elem" v-on:click="patchComment">
                <i class="material-symbols-outlined">
                    ios_share
                </i>
            </div>
        </div>
        <div v-bind:class="[{ display: !creatCommentVal }, { hiden: creatCommentVal }]">
            <commentCreate />
        </div>
    </div>
</template>
<script>
import DatePicker from 'vue2-datepicker';
import 'vue2-datepicker/index.css';
import commentCreate from './commentCreate.vue'

const default_layout = "default";

export default {

    methods: {
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
}
</script>
<style>

</style>