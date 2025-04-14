<template>
    <div class="pokemon-listin">
        <b-button v-for="pokemon in pokemonList" @click="savePage(pokemon)"  :key="pokemon.id">
            <div class="pokemon-card" :class="`pokemon-card-${pokemon.color.name}`">
                <img class="poke-card-img" :src="`https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/${pokemon.id}.png`" alt="">
                <h3 class="pokemon-name">{{ pokemon.name}}</h3>
                <p class="pokemon-habitat">Reside en: <span class="habitat-name">{{ pokemon.pokemon_v2_pokemonhabitat === null ? "Habitat desconocido" : pokemon.pokemon_v2_pokemonhabitat.name}}</span></p>
            </div>

        </b-button>
    </div>
</template>


<script>
export default {
  name: 'charCardList',
  props: {
    pokemonList: Array,
    currentPage: Number,

  },mounted(){
    console.log(this.currentPage);

  }, methods:{
    savePage(pokemon){
      console.log("Hola");
      localStorage.setItem('current', parseInt(this.currentPage));
      this.$router.push(`/character/${pokemon.color.name}/${pokemon.id}`);
    },
    async getColorCards(){
        try {
            const query =  `
                query{
                    pokemon_v2_pokemoncolorname{
                        id
                        name
                        pokemon_color_id

                    }
                }
            `
        } catch (error) {

        }
    }
  }
}
</script>

<style>
    .pokemon-listin{
        display: flex;
        flex-wrap: wrap;
        justify-content: space-evenly;
        flex-direction: row;
        gap: 20px;
    }

    .pokemon-card{
        color: black;
        display: flex;
        flex-direction: column;
        gap: 10px;
        width: 250px;
        height: 280px;
        justify-content: space-evenly;
        align-items: center;
        border-radius: 20px;
        opacity: .8;
    }
    .pokemon-card:hover{
        opacity: 1;
        color: white;
    }


    .pokemon-card-green{
        background-color:  rgb(134, 185, 134);
    }
    .pokemon-card-blue{
        background-color:  rgb(92, 148, 201);
    }
    .pokemon-card-red{
        background-color:  rgb(202, 76, 76);
    }
    .pokemon-card-white{
        background-color:  rgb(219, 219, 219);
    }
    .pokemon-card-purple{
        background-color:  rgb(168, 128, 196);
    }
    .pokemon-card-brown{
        background-color:  rgb(165, 142, 80);
    }
    .pokemon-card-yellow{
        background-color:  rgb(218, 203, 75);
    }
    .pokemon-card-pink{
        background-color:  rgb(207, 89, 182);
    }
    .pokemon-card-gray{
        background-color:  rgb(151, 151, 151);
    }
    .pokemon-card-black{
        background-color:  rgb(53, 53, 53);
        color: white;
    }


    .poke-card-img{
        max-width: 100px;
    }

    .btn{
        background-color: transparent;
        padding: 0px;
        border: none;
    }
    .btn:hover{
        background-color: transparent;
    }

    .habitat-name{
        font-weight: bold;
    }

</style>
