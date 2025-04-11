<template>
  <div class="container">
    <div class="filtering">
      Filtros
    </div>
    <div class="content">
      <div>
        <b-pagination v-model="currentPage" :total-rows="rows" :per-page="limit" aria-controls="my-table"></b-pagination>
        <p>Page: {{ currentPage }}</p>
      </div>
      <charCardList :pokemonList="pokemons" />
      <NuxtLink :to="`/character/${this.id}`">Ir a página del personaje</NuxtLink>
    </div>


  </div>
</template>

<script>

import charCardList from "./components/characterList.vue"
import axios from "axios"

export default {
  components: {
    charCardList,
  },
  data() {
    return {
      pokemons: [],
      imaged: [],
      id: 5,
      currentPage: 1,
      limit: 10,
      total: 0,
    }
  }, mounted() {
    this.fetchCharacters()
  }, watch: {
    currentPage() {
      console.log("CurrentPage cambió: ", this.currentPage);
      this.fetchCharacters(this.currentPage)
    }
  },
  computed: {
    rows() {
      return this.total;
    }
  },
  methods: {
    async fetchCharacters() {

      //console.log("Antes", this.total);
      //this.total = 0;
      //console.log("Despues", this.total);

      try {
        const query = `
              query{
                gen3_species: pokemon_v2_pokemonspecies(
                  order_by: {id: asc}
                  ${this.currentPage ? `offset: ${(this.currentPage * this.limit) - this.limit},` : 0}
                  ${this.limit ? `limit: ${this.limit},` : 3}
                ) {
                  id
                  capture_rate
                  name
                  pokemon_v2_pokemons {
                    name
                    weight
                    height
                  }
                  is_baby
                  is_legendary
                  is_mythical
                  pokemon_v2_pokemonhabitat {
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
        console.log(responseGraph);
        console.log(responseGraph.data);
        console.log(responseGraph.data.data);
        console.log("a", responseGraph.data.data.gen3_species);
        console.log("b", responseGraph.data.data.generations[0]);
        console.log("c", responseGraph.data.data.generations[0].pokemon_species.aggregate.count);
        console.log("Images", responseGraph.data.data.images);
        let sum = 0;
        console.log(this.pokemons);
        this.pokemons = responseGraph.data.data.gen3_species;


        for (let i = 0; i < responseGraph.data.data.generations.length; i++) {
          sum += responseGraph.data.data.generations[i].pokemon_species.aggregate.count;
        }

        this.total = sum;
        console.log("total ", this.total);
      } catch (error) {
        console.log(error);
      }

    },
    linkGen(pageNum) {
      return pageNum === 1 ? '?' : `?page=${pageNum}`
    }
  }
}
</script>

<style>

  body{
    width: 100%;
  }

  .container{
    width: 100%;
    display: flex;
    flex-direction: row;
    gap:40px;
    margin: 0px;
    justify-content: space-between;
  }

  .filtering{

  }

  .content{
    display: flex;
    flex-direction: column;
    gap: 20px;
  }

</style>
