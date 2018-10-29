<template>
    <div class="new-design mandragora">
      <div class="container">
        <div id="content" class="col-sm-12 col">
          <div class="menu-header">
                <props-component :title="$store.state.head" :name="$store.state.inputType"/>
                <button id="sBu" type="button" v-on:click="clickButton()">
                    <img src="/static/imgs/chevron-icon.svg" class="chevron_icon" v-bind:class="{ animate_chevron: $store.state.menuOpened }">
                   <span class="textSbu">
                        {{getSelected}}
                    </span>
                </button>
                <div id="itemsBody" v-if="$store.state.menuOpened">
                    <div class="content_head">
                        <img src="/static/imgs/search-icon.svg" class="search_icon">
                        <input id="iBu" class="form-control" type="text" v-model="$store.state.search" placeholder="Поиск">
                    </div>

                    <div class="clear line"></div>
                    <div class="content">
                        <div class="col-sm-12">
                            <div class="custom-control custom-checkbox mt-3" v-for="(key, value) in filteredList" :key="key.id">
                                <input class="custom-control-input" type="checkbox" :id="'customCheck'+key.id" :value="key.id" v-on:click="toggleSelect(key.id, value)" v-model="listItems" :checked="key.selected">
                                <label class="custom-control-label" :for="'customCheck'+key.id">{{key.name}}</label>
                            </div>
                        </div>
                    </div>
                    
                    <div class="line clear"></div>
                    <div class="action">
                        <label>
                            <input v-if="!$store.state.selectAction" type="checkbox" id="sAll" class="switchButton form-control" value="Отметить все" v-model="select">
                            <input v-else type="checkbox" id="sAll" class="switchButton form-control" value="Отменить все" v-model="unselect">
                        </label>
                    </div>
                </div>
            </div>
        </div>
      </div>
    </div>
</template>

<script>

    // Initiate component

    import Vue from 'vue'
    import Vuex from 'vuex';
    /*
        Vuex is a state management pattern + library for Vue.js applications
        It serves as a centralized store for all the components in an application
    */
    Vue.use(Vuex);
    
    import PropsComponent from './PropsComponent.vue';
    
    const store = new Vuex.Store({
        /*
            Define a few store variables for this component
        */
        state: {
            head: "Города",
            inputType: "city",
            maxWords: 500, // The number of max words
            texts : [], // List of all the generated words
            letters : "абвгдеёжзийклмнопрстуфхцчшщъыьэюя", // Russian alphabets
            wordLength : Math.floor(Math.random() * 7) + 3, // Random length number of each word to be generated
            search: '', // Model variable that will save the search input value
            min_symbols: 3, // The minimum number of words to be typed to begin the search
            selectedItems: 0, // How many items have been selected
            menuOpened: false, // Boolean variable value to check if the list menu is opened or closed
            selectAction: false, // Boolean variable to check if all items are selected
            username: 'Нуждин Вячеслав' // Default app username
        },
        actions: {
            generate: state => {
                // Generate random words
                for (var i = 1; i < store.state.maxWords; i++){
                    let word = "";
                    for(var j = 1; j < store.state.wordLength; j++) {
                        word += store.state.letters.charAt(Math.floor(Math.random() * store.state.letters.length));
                    }
                    store.state.texts.push(
                        {
                            id:i,
                            name:word,
                            selected: false
                        }
                    )
                }
                store.state.selections = store.state.texts;
                return store.state.selections;
            }
        }
    });

    export default {
        name: 'cities-dropdown',
        components: {
            PropsComponent
        },
        // Include store with component
        store: store,
        data () {
            return {
                selections: null,
                listItems: []
            };
        },
        mounted: function() {
            // This component just got created
            // promise have to be returned.
            store.dispatch("generate").then(data => {
                this.selections = data;
            }, error => {
                this.selections = [];
            })
        },
        methods: {
            // Toggling the menu open and close
            clickButton: function(){
                store.state.menuOpened = !store.state.menuOpened
            },
            // Get all the selected items from the menu
            getSelectedItemsLength (){
                return this.selections.filter((x,i) => { return x.selected ? x.selected = true: 0; }).length;
            },
            // Get all the unselected items from the menu
            getNotSelectedItemsLength (){
                return this.selections.filter((x,i) => { return x.selected ? x.selected = false: 0; }).length;
            },
            // Swtich the checked status of each item between on and off
            toggleSelect: function(index, id){
                var allItems = [];
                if(typeof allItems[this.selections[index-1]] == 'undefined'){
                    if(this.selections[index-1].selected == true){
                        this.selections[index-1].selected = false;
                        allItems.splice(this.selections[index-1].id);
                        return store.state.selectedItems = this.getNotSelectedItemsLength;
                    } else {
                        this.selections[index-1].selected = true;
                        allItems.push(this.selections[index-1].id);
                        return store.state.selectedItems = this.getSelectedItemsLength;
                    }
                    this.listItems = allItems;
                }
            }
        },
        computed: {
            // Check all items inside menu
            select: {
                get: function(){
                    return this.selections ? this.listItems.length == this.selections.length: false;
                },
                set: function(value){
                    var allItems = [];
                    if(value){
                        if(this.selections.length != this.filteredList.length){
                            this.filteredList.forEach(function(item){
                                item.selected = true;
                                allItems.push(item.id);
                            });
                        } else {
                            this.selections.forEach(function(item){
                                item.selected = true;
                                allItems.push(item.id);
                            });
                        }
                        if(!store.state.selectAction){
                            store.state.selectAction = true;
                        }
                        this.listItems = allItems;
                    }
                    return this.selectedItems = this.getSelected;
                }
            },
            // Uncheck all items inside menu
            unselect: {
                get: function(){
                    return this.selections ? this.listItems.length == this.selections.length: false;
                },
                set: function(value){
                    var allItems = this.listItems;
                    if(this.selections.length != this.filteredList.length){
                        this.filteredList.forEach(function(item){
                            allItems.splice(item.id);
                            item.selected = false;
                        });
                    } else {
                        this.selections.forEach(function(item){
                            allItems.splice(item.id);
                            item.selected = false;
                        });
                        this.listItems = [];
                    }
                    if(store.state.selectAction){
                        store.state.selectAction = false;
                    }
                    this.listItems = [];
                    return this.selectedItems = this.getSelected;
                }
            },
            // Get inputs and filter them out to search inside the menu list for any matchs
            filteredList() {
                if(store.state.search.length > store.state.min_symbols){
                    // A soft selection to sort out just the needed results
                    return this.selections.filter(item => {
                        store.state.selectedItems = this.getSelected;
                        return item.name.toLowerCase().includes(store.state.search.toLowerCase());
                    })
                } else {
                    // Return empty list in case of 0 results
                    store.state.selectedItems = this.getSelected;
                    return this.selections;
                }
            },
            // Selected items
            getSelected(){
                // Check the length of list items
                let textOut = 'Ничего не выбрано';
                if(this.listItems.length > 0){
                    textOut = `Выбрано: ${this.listItems.length}`;
                }
                return textOut;
            },
            // Object contains all the selected items to send to Back-End server
            dataBaseObject (){
                return this.selections.filter((x,i) => { return x.selected ? x.selected = true: false; return [{}]; });
            },
        }
    }
</script>

<style lang="scss">
    @import "../../assets/styles";
</style>
