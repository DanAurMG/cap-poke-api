<template>
    <div :class="`pokemon-page-${this.color}`" class="pokemon-page">
        <NuxtLink class="boton-back" :to="`/`">Regresar al listado</NuxtLink>
        <div class="poke-page-container">
        <div class="img-container"><img class="poke-img" :src="`https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/${this.id}.png`" alt=""></div>
        <h2 class="poke-name">{{ this.pokemon.name }} </h2>

        <h4 class="info-sect">Información útil para los que comienzan a adentrarse en el mundo de Pokemon</h4>
        <p> La tasa de captura de este pokemon es de: {{ this.pokemon.capture_rate }} </p>
        <p>¿Puedes encontar a este pokemon como bebé? {{ !this.pokemon.is_baby ? "No" : "Si"}}</p>
        <p>¿Es este un pokemon legendario? {{ this.pokemon.is_legendary ? "Si" : "No"}}</p>
        <p>¿Este pokemon es de calidad "Mítico"? {{ this.pokemon.is_mythical ? "Si" : "No"}}</p>
        <p class="pokemon-habitat">Peso del pokemon: {{ !this.pokemon.pokemon_v2_pokemons ? "Peso desconocido" :
            this.pokemon.pokemon_v2_pokemons[0].weight }} kilogramos</p>
        <p class="pokemon-habitat"> Altura del pokemon: {{ !this.pokemon.pokemon_v2_pokemons ? "Altura desconocido" :
            this.pokemon.pokemon_v2_pokemons[0].height }} pies</p>
        <p class="pokemon-habitat"> Habitat del pokemon: {{ !this.pokemon.pokemon_v2_pokemonhabitat ? "Habitat desconocido" :
            pokemon.pokemon_v2_pokemonhabitat.name }}</p>
        </div>
    </div>
</template>

<script>
import axios from 'axios'
export default {
    name: 'charpageList',

    data() {
        return {
            id: 1,
            color: "",
            pokemon: [],

        }
    }, mounted() {
        this.id = this.$route.params.id;
        this.color = this.$route.params.color;
        setTimeout(() => {
            this.getPokemon();
        }, 500)

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
                            pokemon_v2_pokemonabilities {
                                pokemon_v2_ability {
                                    name
                                }
                            }
                        }
                        pokemon_v2_pokemonhabitat {
                            name
                        }
                        color: pokemon_v2_pokemoncolor {
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
            console.log(this.pokemon.color.name);
            console.log(this.pokemon.color.name);

        }
    }
}
</script>

<style>

.poke-img {
    width: 300px;
}

.boton-back:hover{
    text-decoration: none;
}

.poke-name{
    text-transform: capitalize;
}

.boton-back{
    background-color: white;
    border: solid black;
    font-weight: bold;
    color: black;
    border-radius: 10px;
    padding: 10px;
    margin-top: 30px;
}
.info-sect{
    margin: 30px 0px;
}

.img-container{
    margin: 20px 0px;
    background-color: beige;
    border: 5px black solid;
    border-radius: 20px;

}

.pokemon-page{
    height: 100%;
    display: flex;
    flex-direction: column;
    justify-content: space-evenly;
    align-items: center;
}
.poke-page-container{

    display: flex;
    flex-direction: column;
    justify-content: space-evenly;
    align-items: center;
}

.pokemon-page-green {
    background-color: rgb(134, 185, 134);
}

.pokemon-page-blue {
    background-color: rgb(92, 148, 201);
}

.pokemon-page-red {
    background-color: rgb(202, 76, 76);
}

.pokemon-page-white {
    background-color: rgb(219, 219, 219);
}

.pokemon-page-purple {
    background-color: rgb(168, 128, 196);
}

.pokemon-page-brown {
    background-color: rgb(165, 142, 80);
}

.pokemon-page-yellow {
    background-color: rgb(218, 203, 75);
}

.pokemon-page-pink {
    background-color: rgb(207, 89, 182);
}

.pokemon-page-black {
    background-color: rgb(53, 53, 53);
    color: white;

}
.pokemon-page-gray {
    background-color: rgb(151, 151, 151);
}
</style>
