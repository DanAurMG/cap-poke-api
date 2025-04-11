<template>
    <div>

        <NuxtLink :to="`/`">Regresar</NuxtLink>

        <img :src="`https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/${this.id}.png`" alt="">
        {{ this.pokemon.name }}
        {{ this.pokemon.capture_rate }}
        Es bebé {{ this.pokemon.is_baby }}
        Es legendario {{ this.pokemon.is_legendary }}
        Es Mítico {{ this.pokemon.is_mythical }}
        <p class="pokemon-habitat">{{ !this.pokemon.pokemon_v2_pokemons ? "Peso desconocido" :
            this.pokemon.pokemon_v2_pokemons[0].weight}}</p>
        <p class="pokemon-habitat">{{ !this.pokemon.pokemon_v2_pokemons ? "Altura desconocido" :
            this.pokemon.pokemon_v2_pokemons[0].height}}</p>
        <p class="pokemon-habitat">{{ !this.pokemon.pokemon_v2_pokemonhabitat ? "Habitat desconocido" :
            pokemon.pokemon_v2_pokemonhabitat.name}}</p>


    </div>
</template>

<script>
import axios from 'axios'
export default {
    name: 'charCardList',

    data() {
        return {
            id: 1,
            pokemon: [],

        }
    }, mounted() {
        this.id = this.$route.params.id;
        console.log(this.id);
        this.getPokemon();

    },
    methods: {

        async getPokemon() {
            const query = `
              query{
                gen3_species: pokemon_v2_pokemonspecies(where: {id: {_eq: ${this.id}}}){
                    name
                    capture_rate
                    base_happiness
                    is_baby
                    is_legendary
                    is_mythical
                    has_gender_differences
                    pokemon_v2_pokemons {
                        weight
                        height
                        pokemon_v2_pokemontypepasts {
                        pokemon_v2_type {
                        id
                        }
                    }
                    pokemon_v2_pokemonabilities {
                        pokemon_v2_ability {
                        name
                        }
                    }
                    }
                    pokemon_v2_pokemonhabitat {
                        name
                    }
                  }
                }
            `;
            const responseGraphPoke = await axios({
                url: "https://beta.pokeapi.co/graphql/v1beta",
                method: 'post',
                data: {
                    query
                }
            });
            console.log(responseGraphPoke);
            console.log(responseGraphPoke.data);
            console.log(responseGraphPoke.data.data);
            console.log(responseGraphPoke.data.data.gen3_species);
            this.pokemon = responseGraphPoke.data.data.gen3_species[0];
        }
    }
}
</script>

<style>
img {
    width: 500px;
}
</style>