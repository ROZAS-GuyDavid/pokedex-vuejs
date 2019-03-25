<template>  
    <div class="row">
        <div id="results-container">
            <div class="col s12">
                <div class="card col s3 red darken-3" v-for="(pokemon,index) in pokemons" >
                    <div class="card-image">
                        <img :src=pokemons[index].sprites.front_default>               
                    </div>
                    <div class="card-content">
                        <p><span class="card-title center-align">{{pokemons[index].name}}</span></p>
                        <p><span>#{{pokemons[index].id}}</span></p>
                        <p><span>Type: </span>{{allTypes(index)}}</p>
                        <p>git test3</p>
                    </div>
                </div> 
            </div>
            <preloader id ="preloader" v-if="preloaderAnim"></preloader>
        </div>
    </div>
</template>


<script>
import Preloader from './Preloader.vue'
export default {
    components: { Preloader },
    data () {
        return {
            pokemons: [],
            idx: 0,
            preloaderAnim: false,
        }
    },
     methods: {
         getLinks: function(){
             
             var idx = this.idx;
             var limit = idx+20;

             axios
            .get('https://pokeapi.co/api/v2/pokemon/?offset=20&limit='+limit)
            .then(response => {
                var links = [] //array of url
                for(idx; idx<limit;idx++){
                    links.push(response.data.results[idx].url)
                }
                this.idx = idx;  // MAJ my component variable
                this.listingPokemon(links) // get a object of pokemon
            });
         },
        listingPokemon: function(links){
            var pokemons = this.pokemons;  // init my array of pokemon objects
            var pokemon = links.map(function(link){ 
                // make a request for all value of links
                return axios
                    .get(link)
                    .then(response => pokemons.push(response.data)) // push array of pokemon objects
            })
            this.pokemons = pokemons; // MAJ my component variable
        },
        allTypes:function(index){
            var i = 0;
            var types = this.pokemons[index].types;
            var names = types[i].type.name;

            var typesTab = [];
            for (i = 0; i<types.length; i++) { 
                // make a array of all pokemon types
                typesTab.push(names);
            }
            typesTab = typesTab.toString();

            return typesTab;
        },
        handleScroll (event) {
            var d = document.documentElement;
            var offset = d.scrollTop + window.innerHeight; //scrolltop + device height
            var height = d.offsetHeight; // page height

            if (offset === height) { //if scrolltop + device height === page height
                this.getLinks(); // start process for take new pokemons
            }
        }
    },
    created () {
        window.addEventListener('scroll', this.handleScroll); // listen the scroll and send a event to handleScroll 

        this.getLinks(); // start process for take new pokemons

        axios.interceptors.request.use((config) => {
            // trigger 'loading=true' event here
                this.preloaderAnim =!this.preloaderAnim
            return config;
        }, (error) => {
            // trigger 'loading=false' event here
            return Promise.reject(error);
        });

        axios.interceptors.response.use((response) => {
            // trigger 'loading=false' event here
            return response;
        }, (error) => {
            // trigger 'loading=false' event here
            return Promise.reject(error);
        });

  }
}
</script>
