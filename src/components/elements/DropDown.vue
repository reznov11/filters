<template>
    <div class="new-design mandragora">
      <div class="container">
        <div id="content" class="col-sm-12 col">
          <div class="menu-header">
                <dropTitle :title="head"/>
                <button id="sBu" type="button" v-on:click="clickButton()">
                    <img src="/static/imgs/chevron-icon.svg" class="chevron_icon" v-bind:class="{ animate_chevron: menuOpened }">
                    <span class="textSbu" v-if="selectedItems == 0">
                        Не выбрано
                    </span>
                    <span class="textSbu" v-else>
                        Выбрано: {{getSelected}}
                    </span>
                </button>
                <div id="itemsBody" v-if="menuOpened">
                    <div class="content_head">
                        <img src="/static/imgs/search-icon.svg" class="search_icon">
                        <input id="iBu" class="form-control" type="text" v-model="search" placeholder="Поиск">
                    </div>

                    <div class="clear line"></div>
                    <div class="content">
                        <div class="col-sm-12">
                            <dropType :name="inputType"/>
                            <div class="custom-control custom-checkbox mt-3" v-for="(key, value) in filteredList" :key="key.id">
                                <input class="custom-control-input" type="checkbox" :id="'customCheck'+key.id" :value="key.id" v-on:click="toggleSelect(key.id, value)" v-model="listItems" :checked="key.selected">
                                <label class="custom-control-label" :for="'customCheck'+key.id">{{key.name}}</label>
                            </div>
                        </div>
                    </div>
                    
                    <div class="line clear"></div>
                    <div class="action">
                        <label>
                            <input v-if="!selectAction" type="checkbox" id="sAll" class="switchButton form-control" value="Отметить все" v-model="select">
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

    const dropTitle = {
        template: '<h1>{{title}}</h1>',
        props: {
            title: {
                type: String,
                required: true
            }
        },
    }
    const dropType = {
        template: '<input type="hidden" :value="name"/>',
        props: {
            name: {
                type: String,
                required: true
            }
        },
    }

    // Initiate
    export default {
        components: {
            dropTitle,
            dropType
        },
        data () {
            /*
                Define a few variables to handle the generation of words in russian alphabets
            */
            let maxWords = 500 ; // The number of max words
            let texts = []; // List of all the generated words
            let letters = "абвгдеёжзийклмнопрстуфхцчшщъыьэюя"; // Russian alphabets
            let wordLength = Math.floor(Math.random() * 7) + 3; // Random length number of each word to be generated
            
            // Generate random words
            for (var i = 1; i < maxWords; i++){
                let word = "";
                for(var j = 1; j < wordLength; j++) {
                    word += letters.charAt(Math.floor(Math.random() * letters.length));
                }
                texts.push(
                    {
                        id:i,
                        name:word,
                        selected: false
                    }
                )
            }

            let randomWords = texts // Assign the words list to the data object variable randomWords

            // Return data object
            return {
                head: "Города",
                inputType: "city",
                search: '', // Model variable that will save the search input value
                min_symbols: 3, // The minimum number of words to be typed to begin the search
                selectedItems: 0, // How many items have been selected
                menuOpened: false, // Boolean variable value to check if the list menu is opened or closed
                selectAction: false, // Boolean variable to check if all items are selected
                listItems: [], // A list that will contain all the selected items by clicking the select all button
                username: 'Нуждин Вячеслав', // Default app username
                selections: randomWords
            }
        },
        methods: {
            // Toggling the menu open and close
            clickButton: function(){
                if(this.menuOpened){
                    this.menuOpened = false;
                } else {
                    this.menuOpened = true;
                }
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
                        return this.selectedItems = this.getNotSelectedItemsLength;
                    } else {
                        this.selections[index-1].selected = true;
                        allItems.push(this.selections[index-1].id);
                        return this.selectedItems = this.getSelectedItemsLength;
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
                        if(!this.selectAction){
                            this.selectAction = true;
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
                    if(this.selectAction){
                        this.selectAction = false;
                    }
                    this.listItems = [];
                    return this.selectedItems = this.getSelected;
                }
            },
            // Get inputs and filter them out to search inside the menu list for any matchs
            filteredList() {
                if(this.search.length > this.min_symbols){
                    // A soft selection to sort out just the needed results
                    return this.selections.filter(item => {
                        this.selectedItems = this.getSelected;
                        return item.name.toLowerCase().includes(this.search.toLowerCase());
                    })
                } else {
                    // Return empty list in case of 0 results
                    this.selectedItems = this.getSelected;
                    return this.selections;
                }
            },
            // Selected items
            getSelected(){
                /*
                    Check the length of each items list

                    first case: return the length of all checked items
                    second case: return the length of all checked items sorted by input search results
                */
                switch (this.listItems.length > 0){
                    case true:
                        return this.selectedItems = this.listItems.length;
                    case false:
                        return this.selectedItems = 0;
                }
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
