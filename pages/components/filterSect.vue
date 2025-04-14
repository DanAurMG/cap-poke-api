<template>
    <div class="filter-container">
        <b-button class="button-filter-del" @click="delFilter">Eliminar los filtros</b-button>
        <b-button class="button-filter-del" @click="applyFilters">Buscar</b-button>
        <div class="filter-name filter-sect">
            <label class="filter-sect-title" for="nombre">
                ¡Busca por nombre a tu pokemon!</label>
            <input type="text" v-model="filter.name"  v-on:keyup.enter = "applyFilters" v-on:keyup="applyFilters" placeholder="Ingresa un nombre" id="nombre">

        </div>
        <div class="filter-color filter-sect" >
            <p class="filter-sect-title">Color del pokemon:</p>
            <div class="checks-sect">
                <div>
                    <input v-model="filter.color" value="green" type="checkbox" name="verde" id="verde">
                    <label for="verde">Verde</label>
                </div>
                <div>
                    <input v-model="filter.color" value="red" type="checkbox" name="rojo" id="rojo">
                    <label for="rojo">rojo</label>
                </div>
                <div>
                    <input v-model="filter.color" value="blue" type="checkbox" name="azul" id="azul">
                    <label for="azul">azul</label>
                </div>
                <div>
                    <input v-model="filter.color" value="yellow" type="checkbox" name="amarillo" id="amarillo">
                    <label for="amarillo">amarillo</label>
                </div>
                <div>
                    <input v-model="filter.color" value="white" type="checkbox" name="blanco" id="blanco">
                    <label for="blanco">blanco</label>
                </div>
                <div>
                    <input v-model="filter.color" value="brown" type="checkbox" name="cafe" id="cafe">
                    <label for="cafe">café</label>
                </div>
                <div>
                    <input v-model="filter.color" value="pink" type="checkbox" name="rosa" id="rosa">
                    <label for="rosa">rosa</label>
                </div>
                <div>
                    <input v-model="filter.color" value="purple" type="checkbox" name="morado" id="morado">
                    <label for="morado">morado</label>
                </div>
                <div>
                    <input v-model="filter.color" value="gray" type="checkbox" name="gris" id="gris">
                    <label for="gris">gris</label>
                </div>
                <div>
                    <input v-model="filter.color" value="black" type="checkbox" name="negro" id="negro">
                    <label for="negro">negro</label>
                </div>
            </div>

        </div>
        <div class="filter-weight filter-sect">
            <p class="filter-sect-title">Peso del pokemon:</p>
            <div class="checks-sect">
                <div>
                    <input type="checkbox" name="ligero" id="ligero">
                    <label for="ligero">Ligero</label>
                </div>
                <div>
                    <input type="checkbox" name="lAlto" id="meidano">
                    <label for="meidano">Mediano</label>
                </div>
                <div>
                    <input type="checkbox" name="pesado" id="pesado">
                    <label for="pesado">Pesado</label>
                </div>
                <div>
                    <input type="checkbox" name="mPesado" id="mPesado">
                    <label for="mPesado">Muy pesado</label>
                </div>
            </div>
        </div>

        <div class="filter-height filter-sect">
            <p class="filter-sect-title">Talla del pokemon:</p>
            <div class="checks-sect">
                <div>
                    <input type="checkbox" name="pequeño" id="pequeño">
                    <label for="pequeño">Pequeño</label>
                </div>
                <div>
                    <input type="checkbox" name="lAlto" id="lAlto">
                    <label for="lAlto">Mediano</label>
                </div>
                <div>
                    <input type="checkbox" name="alto" id="alto">
                    <label for="alto">Alto</label>
                </div>
                <div>
                    <input type="checkbox" name="mAlto" id="mAlto">
                    <label for="mAlto">Muy alto</label>
                </div>
            </div>

        </div>
        <div class="filter-rarity filter-sect">
            <p class="filter-sect-title">Rareza del pokemon:</p>
            <div class="checks-sect">
                <div>
                    <input type="checkbox" name="comun" id="comun">
                    <label for="comun">Común</label>
                </div>
                <div>
                    <input type="checkbox" name="legendario" id="legendario">
                    <label for="legendario">Legendario</label>
                </div>
                <div>
                    <input type="checkbox" name="mitico" id="mitico">
                    <label for="mitico">Mítico</label>
                </div>
            </div>

        </div>


    </div>
</template>

<script>
export default{
    name: 'filterSection',

    data(){
        return {
            filter: {
                name: '',
                color: [],
                weight: [],
                height: [],
                rarity: [],

            }
        }
    }, methods: {
        applyFilters(){
          console.log("Segun en string ", JSON.stringify(this.filter.color));

          this.$emit("filter-pokemon", {...this.filter})
        },delFilter(){
            this.filter.name= "";
            this.filter.color= "";
            this.filter.weight= "";
            this.filter.height= "";
            this.filter.rarity= "";
            this.$emit("del-filters")
        }
    }

}
</script>

<style>

    .filter-container{
        display: flex;
        flex-direction: column;
        align-items: center;
        gap: 10px;
    }

    .filter-name{
        padding: 30px 0px 30px 30px;
        background-color: rgb(88, 153, 197);
        border-top-right-radius: 10px;
        border-bottom-right-radius: 10px;

    }

    .button-filter-del{
        background-color: rgb(230, 112, 112);
        color: black;
        border: solid black 3px;
        border-radius: 20px;
        padding: 10px;
        font-weight: bold;
    }
    .button-filter-del:hover{
        background-color: rgb(223, 59, 59);
        color: white;
    }

    .filter-sect-title{
        font-weight: bold;
        font-family: 'Courier New', Courier, monospace;
        font-size: large;
    }

    .filter-color{
        display: flex;
        flex-direction: column;
        gap: 10px;
        background-color: burlywood;
        margin-top: 20px;
        border-top-right-radius: 10px;
        border-bottom-right-radius: 10px;
        padding: 20px 0px 20px 30px;
    }

    .filter-weight{
        display: flex;
        flex-direction: column;
        gap: 10px;
        background-color: rgb(181, 222, 135);
        margin-top: 20px;
        border-top-right-radius: 10px;
        border-bottom-right-radius: 10px;
        padding: 20px 0px 20px 30px;
    }

    .filter-rarity{
        display: flex;
        flex-direction: column;
        gap: 10px;
        width: 100%;
        background-color: rgb(168, 135, 222);
        margin-top: 20px;
        border-top-right-radius: 10px;
        border-bottom-right-radius: 10px;
        padding: 20px 0px 20px 30px;
    }

    .filter-height{
        display: flex;
        flex-direction: column;
        gap: 10px;
        background-color: rgb(222, 135, 142);
        margin-top: 20px;
        border-top-right-radius: 10px;
        border-bottom-right-radius: 10px;
        padding: 20px 0px 20px 30px;
    }

    .checks-sect{
        display: flex;
        flex-direction: row;
        gap: 20px;
        flex-wrap: wrap;
    }

    .filter-sect{
        width: 100%;
    }

</style>
