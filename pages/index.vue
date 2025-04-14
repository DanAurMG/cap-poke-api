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
      userfilters: {},
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
    async fetchCharacters() {
      try {
        const query = `
              query{
                gen3_species: pokemon_v2_pokemonspecies(
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
                }generations: pokemon_v2_generation{
                  pokemon_species: pokemon_v2_pokemonspecies_aggregate {
                    aggregate {
                      count
                    }
                  }
                }
                images: pokemon_v2_pokemonformsprites {
                  sprites(path: "front_default")
                  id
                }
              }
            `

        const responseGraph = await axios({
          url: "https://beta.pokeapi.co/graphql/v1beta",
          method: 'post',
          data: {
            query
          }
        });
        let sum = 0;
        this.pokemons = responseGraph.data.data.gen3_species;

        for (let i = 0; i < responseGraph.data.data.generations.length; i++) {
          sum += responseGraph.data.data.generations[i].pokemon_species.aggregate.count;
        }
        console.log(this.pokemons);


        this.total = sum;
      } catch (error) {
        console.log(error);
      }

    },
    // ${this.userfilters.color ? `pokemon_v2_pokemoncolor: {name: { in: ${JSON.stringify(this.userfilters.color)}}}` : ``},
    async fetchFilterChars() {
      try {
        console.log("Entra al filtro", this.userfilters.color);


        const query = `
          query{
                gen3_species: pokemon_v2_pokemonspecies(
                  where: {
                    ${this.userfilters.name ? `name: { _regex: ${this.userfilters.name}}` : ``},
                    ${this.userfilters.color ? `pokemon_v2_pokemoncolor: {name: {_in: ${this.userfilters.color}}}` : ``},

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
                    ${this.userfilters.name ? `name: { _regex: ${this.userfilters.name}}` : ``},
                    ${this.userfilters.color ? `pokemon_v2_pokemoncolor: {name: {_in: ${this.userfilters.color}}}` : ``},

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
      console.log("iltro de color recibido", filters.color);
      console.log("stringifiado ", JSON.stringify(filters.color));

      this.userfilters.name = filters.name ? `"${filters.name}"` : '';
      this.userfilters.color = filters.color ? `${JSON.stringify(filters.color)}` : '';
      this.userfilters.weight = filters.weight ? `"${filters.weight}"` : '';
      this.userfilters.height = filters.height ? `"${filters.height}"` : '';
      this.userfilters.rarity = filters.rarity ? `"${filters.rarity}"` : '';
      this.currentPage = 1;
      this.fetchFilterChars();

    },
    delFilters() {
      this.userfilters.name  = "";
      this.userfilters.color = "";
      this.userfilters.weight = "";
      this.userfilters.height = "";
      this.userfilters.rarity = "";
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
