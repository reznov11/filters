<template>
    <div>
        <h1>{{title}}</h1>
        <input type="hidden" :value="name">
    </div>
</template>


<script>
    export default {
        name:"props-cities-dropdown",
        props: {
            title: String,
            name: String
        },
        data(){
            return {};
        }
    }
</script>
