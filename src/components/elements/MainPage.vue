<template>
    <!-- Navbar block -->
    <nav id="navbar">
        <div class="container">
            <div class="logo">
                <img src="/static/imgs/logo_sita.png" alt="Logo sita">
            </div>
        </div>
        <div class="right-menu col-md-2 col">
            <p>
                {{username}}
            </p>
            <small class="muted">{{welcomeMessage}}</small>
        </div>
        <DropDown />
    </nav>
</template>

<script>
import DropDown from './DropDown.vue'
export default {
    name: 'MainPage',
    components: {
        DropDown
    },
    data () {
        return {
            username: 'Вячеслав Нуждин',
            welcomeMessage: 'Привет, Вячеслав',
        }
    }
}
</script>