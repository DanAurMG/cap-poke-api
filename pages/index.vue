<template>
  <div class="container">
    <div class="filtering">
      <filterSection @filter-pokemon="setFilters" @del-filters="delFilters"/>
    </div>
    <div class="content">
      <div>
        <b-pagination v-model="currentPage" :total-rows="rows" :per-page="limit"
          aria-controls="my-table" first-number last-number></b-pagination>
        <p>Page: {{ currentPage }} de {{ Math.ceil(this.total / this.limit) }}</p>
        <div class="tam-pagination">
          <p>¡Puedes elegir cuantos pokemons ver por página!</p>
          <input v-model="limit" type="number" name="limite" id="limit">
        </div>
      </div>
      <charCardList :pokemonList="pokemons" :currentPage="this.currentPage"/>
    </div>
  </div>
</template>

<script>

import charCardList from "./components/characterList.vue"
import filterSection from "./components/filterSect.vue"
import axios from "axios"

export default {
  components: {
    charCardList,
    filterSection,
  },
  data() {
    return {
      pokemons: [],
      image: [],
      id: 5,
      currentPage: localStorage.getItem('current') ? localStorage.getItem('current') : 1,
      limit: localStorage.getItem('limit') ? localStorage.getItem('limit') : 12,
      total: 0,
      userfilters: {
        color: [],
        name: "",
        weight: [],
        height: []
      },
    }
  }, mounted() {
    const pruebaFit = localStorage.getItem('prueba') ? localStorage.getItem('prueba') : 1
    setTimeout(() => {
      this.currentPage = parseInt(pruebaFit);
      console.log("con pruebaFit ",this.currentPage);
      this.reloadPage();
    }, 250)
  }, watch: {
    currentPage() {
      console.log("CurrentPage cambió: ", this.currentPage);
      localStorage.setItem('prueba', this.currentPage);
      this.fetchFilterChars();
    },
    limit() {
      localStorage.setItem('current', 1);
      localStorage.setItem('limit', this.limit);
      this.fetchFilterChars();

    }
  },
  computed: {
    rows() {
      return this.total;
    }
  },
  methods: {
    reloadPage(){
      // localStorage.getItem('current') ? this.currentPage = localStorage.getItem('current') : this.currentPage = 1;
      this.fetchFilterChars();
    },
    // ${this.userfilters.color ? `pokemon_v2_pokemoncolor: {name: { in: ${JSON.stringify(this.userfilters.color)}}}` : ``},
    async fetchFilterChars() {
      try {
        console.log("Entra al filtro", this.userfilters.height);
        console.log( "tamaño de altura", this.userfilters.height.length <= 2);
        console.log( "tamaño de color", this.userfilters.color.length);
        console.log( "tamaño de peso", this.userfilters.weight.length);

        console.log(this.userfilters.height);
        console.log(this.userfilters.color);
        console.log(this.userfilters.weight);
        


        

        const query = `
          query{
                gen3_species: pokemon_v2_pokemonspecies(
                  where: {
                    ${!this.userfilters.name.length === 0 ? `name: { _regex: ${this.userfilters.name}}` : ""},
                    ${!(this.userfilters.color.length <= 2)  ? `pokemon_v2_pokemoncolor: {name: {_in: ${this.userfilters.color}}}` : ""},
                    ${!(this.userfilters.weight.length <= 2) ? `pokemon_v2_pokemons: {_or: ${this.userfilters.weight}}` : ""},
                    ${!(this.userfilters.height.length <= 2) ? `pokemon_v2_pokemons: {_or: ${this.userfilters.height}}` : ""},

                  }
                  order_by: {id: asc}
                  ${this.currentPage ? `offset: ${(this.currentPage * this.limit) - this.limit},` : 1}
                  ${this.limit ? `limit: ${this.limit},` : 12}
                ) {
                  id
                  capture_rate
                  name
                  is_baby
                  is_legendary
                  is_mythical
                  pokemon_v2_pokemons {
                    name
                    weight
                    height
                  }
                  pokemon_v2_pokemonhabitat {
                    name
                  }
                  color: pokemon_v2_pokemoncolor {
                    name
                  }
                }generations: pokemon_v2_pokemonspecies_aggregate(
                  where: {
                    ${!this.userfilters.name.length === 0 ? `name: { _regex: ${this.userfilters.name}}` : ""},
                    ${!(this.userfilters.color.length <= 2)   ? `pokemon_v2_pokemoncolor: {name: {_in: ${this.userfilters.color}}}` : ""},
                    ${!(this.userfilters.weight.length <= 2)  ? `pokemon_v2_pokemons: {_or: ${this.userfilters.weight}}` : ""},
                    ${!(this.userfilters.height.length <= 2) ? `pokemon_v2_pokemons: {_or: ${this.userfilters.height}}` : ""},

                  }
                ){
                  aggregate {
                    count
                  }
                }
              }
        `
        const responseFilteredGraph = await axios({
          url: "https://beta.pokeapi.co/graphql/v1beta",
          method: 'post',
          data: {
            query
          }
        });


        console.log("respuesta filtrada", responseFilteredGraph);
        this.pokemons = responseFilteredGraph.data.data.gen3_species;
        console.log(this.pokemons);
        console.log("Cuenta: ",responseFilteredGraph.data.data.generations.aggregate.count);
        this.total = responseFilteredGraph.data.data.generations.aggregate.count;
      } catch (error) {
        console.log(error);

      }

    },
    setFilters(filters = "") {
      console.log("filtros ", filters);
      console.log("stringifiado peso ", JSON.stringify(filters.weight).replaceAll('"', ''));
      console.log("stringifiado peso ", JSON.stringify(filters.height).replaceAll('"', ''));
      
      // this.userfilters.weight = filters.weight.map(query => JSON.parse(query))
      // console.log("peso parseado, debería funcionar ",JSON.stringify(this.userfilters.weight));

      
      console.log("sin stringifiado peso ", filters.height);

      this.userfilters.name = filters.name ? `"${filters.name}"` : "";
      this.userfilters.color = filters.color ? `${JSON.stringify(filters.color)}` : "";
      this.userfilters.weight = filters.weight ? `${JSON.stringify(filters.weight).replaceAll('"', '')}` : "";
      this.userfilters.height = filters.height ? `${JSON.stringify(filters.height).replaceAll('"', '')}` : "";
      console.log("Filtros", this.userfilters);
      
      this.currentPage = 1;
      this.fetchFilterChars();

    },
    delFilters() {
      this.userfilters.name  = "";
      this.userfilters.color = "";
      this.userfilters.weight = "";
      this.userfilters.height = "";
      console.log(this.userfilters);
      this.fetchFilterChars(1);

    }
  }
}
</script>

<style>
body {
  width: 100%;
}

.container {
  max-width: 100%;
  display: flex;
  flex-direction: row;
  gap: 40px;
  margin: 0px;
  justify-content: space-between;
  padding: 30px 0px;
  background-color: beige;
}

.filtering {
  width: 30%;
}

.tam-pagination input {
  width: 100px;
}

.tam-pagination p {
  margin: 0px;
}

.tam-pagination {
  display: flex;
  flex-direction: row;
  gap: 20px;
  align-items: center;
}

.content {
  display: flex;
  flex-direction: column;
  gap: 20px;
  width: 100%;
}
</style>
