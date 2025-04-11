<template>
    <div class="pokemon-listin">
        <b-button class="pokemon-card" v-for="pokemon in pokemonList" :key="pokemon.id" :to="`/character/${pokemon.id}`">
            <img :src="`https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/${pokemon.id}.png`" alt="">
            <h3 class="pokemon-name">{{ pokemon.name}}</h3>
            <p class="pokemon-habitat">{{ pokemon.pokemon_v2_pokemonhabitat === null ? "Habitat desconocido" : pokemon.pokemon_v2_pokemonhabitat.name}}</p>
        </b-button>
    </div>
</template>


<script>
export default { 
  name: 'charCardList',
  props: {
    pokemonList: Array
  }, methods:{
  }
}
</script>

<style>
    .pokemon-listin{
        display: flex;
        flex-wrap: wrap;
        flex-direction: row;
        gap: 20px;
    }

    .pokemon-card{
        background-color: aqua;
        color: black;
        display: flex;
        flex-direction: column;
        gap: 10px;
        width: 250px;
        height: 200px;
        justify-content: center;
        align-items: center;
    }

    img{
        max-width: 100px;
    }

    .btn{
        background-color: bisque;
    }
</style>