<template>
  <li v-show="display">
    <a v-bind:class="{'isDisabled': disabled, 'is-active':active}" v-bind:href="'?page=' + page"
        v-on:click.prevent="!active && !disabled ? $emit('page-click', page): $event.prevent"><span>{{text}}</span></a>
  </li>
</template>

<script>
export default {
    props: {
        page: {
            type: Number
        },
        disabled: {
            type: Boolean
        },
        active: {
            type: Boolean
        },
        text: {
            type: [String, Number]
        },
        display: {
            type: Boolean,
            default: true
        }
    },
    methods: {
        PageClick(page) {
            this.$emit('page-click', page)
        }
    }
}
</script>

<style scoped>

.responsive-pagination li {
    padding: 0.25rem;
}

.responsive-pagination a {
    -webkit-box-sizing: border-box;
    box-sizing: border-box;
    display: -webkit-box;
    display: -moz-box;
    display: -ms-flexbox;
    display: -webkit-flex;
    display: flex;
    -webkit-box-pack: center;
    -moz-box-pack: center;
    -ms-flex-pack: center;
    -webkit-justify-content: center;
    justify-content: center;
    text-decoration: none;
    border: 1px solid;
    border-color: #2c3e50;
    color: #2c3e50;
    background-color: hsl(0, 0%, 100%);
    min-width: 2.5em;
    margin: 0;
    padding: 0;
}

.responsive-pagination .last-link a,
.responsive-pagination .first-link a {
    min-width: 2.5em;
}

.responsive-pagination a.is-active {
    color: hsl(0, 0%, 100%);
    background-color: #2c3e50;
}

.responsive-pagination.rounded a {
    padding-top: .3em;
    padding-bottom: .3em;
    border-radius: 290486px;
}

.responsive-pagination.square a {
    padding-top: .3em;
    padding-bottom: .3em;
}

.responsive-pagination.semiround a {
    border-radius: 290486px;
}

.responsive-pagination a:hover {
    opacity: 0.9;
}
</style>