<template>
    <div class="comment__body">
        <div class="body__elem">
            <div class="elem__user">
                <i class="material-symbols-outlined">
                    person
                </i>
                <span>
                    {{ this.name }}
                </span>
            </div>
            <div class="elem__id">
                # {{ this.id }}
            </div>
        </div>
        <div class="body__elem text">
            <div class="elem__text">
                <div>{{ this.text }}</div>
            </div>
            <div class="elem__date">
                <div>Дата публикации: {{ this.date }}</div>
            </div>
        </div>
    </div>
</template>
<script>
export default {
    props:[
        'id',
        'name',
        'text',
        'date',
    ]
}
</script>