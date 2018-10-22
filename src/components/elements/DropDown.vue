<template>
    <div class="new-design mandragora">
      <div class="container">
        <div id="content" class="col-sm-12 col">
          <div class="menu-header">
                <h1>{{username}}</h1>
                <button id="sBu" type="button" v-on:click="clickButton()">
                    <img src="/static/imgs/chevron-icon.svg" class="chevron_icon" v-bind:class="{ animate_chevron: menuOpened }">
                    <span class="textSbu" v-if="selectedItems == 0">
                        Не выбрано
                    </span>
                    <span class="textSbu" v-else>
                        Выбрано: {{selectedItems}}
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
                            <div class="custom-control custom-checkbox mt-3" v-for="(key, value) in filteredList" :key="value">
                                <input class="custom-control-input" type="checkbox" :id="'customCheck'+value" :value="key.id" v-on:click="toggleSelect(value, key.id)" v-model="allChecked" :checked="key.selected">
                                <label class="custom-control-label" :for="'customCheck'+value">{{key.name}}</label>
                            </div>
                        </div>
                    </div>
                    
                    <div class="line clear"></div>
                    <div class="action">
                        <label>
                            <input v-if="!selectAll" type="checkbox" id="sAll" class="switchButton form-control" value="Отметить все" v-model="checkAll">
                            <input v-else type="checkbox" id="sAll" class="switchButton form-control" value="Отменить все" v-model="uncheckAll">
                        </label>
                    </div>
                </div>
            </div>
        </div>
      </div>
    </div>
</template>

<script>
    /* eslint-disable */

    // Initiate
    export default {
        name: 'main_menu',
        data () {
            /*
                Define a few variables to handle the generation of words in russian alphabets
            */
            let maxWords = 500 ; // The number of max words
            let texts = []; // List of all the generated words
            let letters = "абвгдеёжзийклмнопрстуфхцчшщъыьэюя"; // Russian alphabets
            let wordLength = Math.floor(Math.random() * 7) + 3; // Random length number of each word to be generated
            
            // Generate random words
            for (var i = 0; i < maxWords; i++){
                let word = "";
                for(var j = 0; j < wordLength; j++) {
                    word += letters.charAt(Math.floor(Math.random() * letters.length));
                }
                texts.push(
                    {
                        "id":i,
                        "name":word,
                        selected: false
                    }
                )
            }

            let randomWords = texts; // Assign the words list to the data object variable randomWords

            // Return data object
            return {
                search: '', // Model variable that will save the search input value
                min_symbols: 3, // The minimum number of words to be typed to begin the search
                selectedItems: 0, // How many items have been selected
                menuOpened: false, // Boolean variable value to check if the list menu is opened or closed
                selectAll: false, // Boolean variable to check if all items are selected
                allChecked: [], // A list that will contain all the selected items by clicking the select all button
                miniSelect: [], // A list that will contain all the selected items depends on search results
                username: 'Нуждин Вячеслав', // Default app username
                randomWords: null, // The generated words
                selections: randomWords 
            }
        },
        methods: {
            // Toggling the menu open and close
            clickButton: function(){
                if(this.menuOpened == true){
                    this.menuOpened = false;
                } else {
                    this.menuOpened = true;
                }
            },

            // Get all the selected items from the menu
            getSelected: function(){
                return this.selections.filter((x,i) => { return x.selected ? x.selected = true: 0; }).length;
            },

            // Get all the unselected items from the menu
            getNotSelected: function(){
                return this.selections.filter((x,i) => { return x.selected ? x.selected = false: 0; }).length;
            },

            // Swtich the checked status of each item between on and off
            toggleSelect: function(index, id){
                if(this.selections[index].selected == true){
                    this.selections[index].selected = false;
                } else {
                    this.selections[index].selected = true;
                }
                return this.selectedItems = this.getSelected();
            },
            // Change the item selected value to true
            addSelect: function(Id){
                return this.selections[Id].selected;
            }
        },
        computed: {
            // Check all items inside menu
            checkAll: {
                get: function(){
                    return this.selections ? this.allChecked.length == this.selections.length: false;
                },
                set: function(value){
                    var allItems = [];
                    if(value){
                        // Check first if miniSelect list contains any items
                        if(this.miniSelect.length > 0){
                            for(var i=0;i<this.miniSelect.length;i++){
                                this.addSelect(this.miniSelect[i]);
                                this.selections[this.miniSelect[i]].selected = true;
                            }
                        } else {
                            // Go through the regular items list
                            this.selections.forEach(function(item){
                                item.selected = true;
                                allItems.push(item.id);
                            });
                            this.allChecked = allItems;
                            this.selectAll = true;
                        }

                        /*
                            Check the length of each items list

                            first case: return the length of all checked items
                            second case: return the length of all checked items sorted by input search results
                        */

                        switch (allItems.length > 0){
                            case true:
                                return this.selectedItems = allItems.length;
                            case false:
                                return this.selectedItems = this.miniSelect.length;
                        }
                    }
                    // return this.selectedItems = this.getSelected();
                }
            },
            // Uncheck all items inside menu
            uncheckAll: {
                get: function(){
                    return this.selections ? this.allChecked.length == this.selections.length: false;
                },
                set: function(value){
                    if(value){
                        this.selections.forEach(function(item){
                            item.selected = false;
                        });
                    }
                    this.allChecked = [];
                    this.selectedItems = this.allChecked.length;
                    this.selectAll = false;
                    return this.selectedItems = this.getNotSelected();
                }
            },
            // Get inputs and filter them out to search inside the menu list for any matchs
            filteredList() {
                if(this.search.length > this.min_symbols){
                    // A soft selection to sort out just the needed results
                    this.miniSelect = [];
                    for(var i=0;i<this.selections.length;i++){
                        if(this.selections[i].name.toLowerCase().includes(this.search.toLowerCase())){
                            if(!this.miniSelect.includes(i)){
                                this.miniSelect.push(this.selections[i].id);
                            }
                        }
                    }
                    this.allChecked = this.miniSelect;
                    // Convert any user uppercase input to lowercase 
                    // to make it not case sensitive
                    return this.selections.filter(item => {
                        return item.name.toLowerCase().includes(this.search.toLowerCase());
                    })
                } else {
                    // Return empty list in case of 0 results
                    this.miniSelect = [];
                    this.selectedItems = 0;
                    return this.selections;
                }
            }
        }
    }
</script>

<style lang="scss">
    @import "../../assets/styles";
</style>
