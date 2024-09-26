<template>
  <div class="container">
    <div class="title">
      <div id="imgSmall">
        <img src="https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcSlvgt4O7bdc3ZlDEvYvma4MmUh_by2nZrV0A&s" alt=""
          class="imgTitle">
      </div>
      <div class="imgPokemon">
        <img
          src="https://upload.wikimedia.org/wikipedia/commons/thumb/9/98/International_Pok%C3%A9mon_logo.svg/640px-International_Pok%C3%A9mon_logo.svg.png"
          alt="" class="titlePrincipal">
      </div>
      <div class="tercerImg" id="imgSmall">
        <img src="https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcS67--0Cm8T67Wa2wCAuS12oRjVLJBIxeyAEA&s" alt=""
          class="imgTitle">
      </div>
    </div>
    <div class="contenido">
      <div class="columna1" :style="columna1Style">
        <h1>{{ nombre }}</h1>
        <img :src="imagenPrincipal" alt="">
        <div v-if="nombre" class="pesoAltura">
          <h2>Peso:{{ pokemonWeigth }}</h2>
          <h2>Altura:{{ pokemonHeight }}</h2>
        </div>
      </div>
      <div class="columna2">
        <p>#{{ idPokemon }}</p>
        <h1>Tipos de pokemon</h1>
        <div class="types">
          <div :style="typePokemon1Style" style="margin-right: 30PX;" id="typePokemon">
            <h3>{{ tipoPokemon1 }}</h3>
          </div>
          <div :style="typePokemon2Style" id="typePokemon" v-if="tipoPokemon2">
            <h3>{{ tipoPokemon2 }}</h3>
          </div>
        </div>
        <div class="fila2_2">
          <h1>Debilidades</h1>
          <div class="weaknesses">
            <div v-for="weakness in weaknesses" :key="weakness" class="weakness" :style="getTypeStyle(weakness)">
              <span>{{ weakness }}</span>
            </div>
          </div>
        </div>
      </div>
      <div class="columna3">
        <h1 v-if="nombre">Stats</h1>
        <div v-if="nombre" class="stats">
          <div class="stat">
            <span>HP:</span> 
            <div class="bar" :style="{ width: calculateBarWidth(stats.hp) }"></div> <span>{{ stats.hp}}/255</span>
          </div>
          <div class="stat">
            <span>Attack:</span>
            <div class="bar" :style="{ width: calculateBarWidth(stats.attack) }"></div> <span>{{ stats.attack}}/255</span>
          </div>
          <div class="stat">
            <span>Defense:</span>
            <div class="bar" :style="{ width: calculateBarWidth(stats.defense) }"></div> <span>{{ stats.defense}}/255</span>
          </div>
          <div class="stat">
            <span>Special Attack:</span>
            <div class="bar" :style="{ width: calculateBarWidth(stats.specialAttack) }"></div> <span>{{ stats.specialAttack}}/255</span>
          </div>
          <div class="stat">
            <span>Special Defense:</span>
            <div class="bar" :style="{ width: calculateBarWidth(stats.specialDefense) }"></div> <span>{{ stats.specialDefense}}/255</span>
          </div>
          <div class="stat">
            <span>Speed:</span>
            <div class="bar" :style="{ width: calculateBarWidth(stats.speed) }"></div> <span>{{ stats.speed}}/255</span>
          </div>
        </div>
      </div>
    </div>
    <div class="columna4">
      <input type="text" v-model="pokemonName" placeholder="pokemon">
      <button @click="listarPokemones(pokemonName)">Buscar</button>
    </div>
  </div>





</template>


<script setup>
import axios from "axios"
import { ref, computed } from "vue";

let nombre = ref("")
let imagenPrincipal = ref("")
let pokemonName = ref("")
let pokemonWeigth = ref("")
let pokemonHeight = ref("")
let idPokemon = ref("")
let tipoPokemon1 = ref("")
let tipoPokemon2 = ref("")
let stats = ref({
  hp: 0,
  attack: 0,
  defense: 0,
  specialAttack: 0,
  specialDefense: 0,
  speed: 0,
});

let weaknesses = ref([]);


async function listarPokemones(pokemon) {
  let url = `https://pokeapi.co/api/v2/pokemon/${pokemon}`
  try {
    let { data } = await axios.get(url)
    console.log(data);
    nombre.value = data.name
    imagenPrincipal.value = data.sprites.other.dream_world.front_default
    pokemonWeigth.value = data.weight / 10
    pokemonHeight.value = data.height / 10
    idPokemon.value = data.id
    tipoPokemon1.value = data.types[0]?.type.name || "";
    tipoPokemon2.value = data.types[1]?.type.name || "";
    stats.value = {
      hp: data.stats[0]?.base_stat || 0,
      attack: data.stats[1]?.base_stat || 0,
      defense: data.stats[2]?.base_stat || 0,
      specialAttack: data.stats[3]?.base_stat || 0,
      specialDefense: data.stats[4]?.base_stat || 0,
      speed: data.stats[5]?.base_stat || 0,
    };
    weaknesses.value = await getWeaknesses(data.types);
  } catch (error) {
    console.error("Error en la petición", error);
    nombre.value = "Pokemon no encontrado"
    imagenPrincipal.value = ""
  }
}

function getBackgroundColor(type) {
  switch (type) {
    case 'fire':
      return 'background-color: red;';
    case 'water':
      return 'background-color: blue;';
    case 'grass':
      return 'background-color: green;';
    case 'fighting':
      return 'background-color: orange;';
    case 'electric':
      return 'background-color: yellow;';
    case 'ice':
      return 'background-color: lightblue;';
    case 'poison':
      return 'background-color: pink;';
    case 'flying':
      return 'background-color: skyblue;';
    case 'psychic':
      return 'background-color: purple;';
    case 'bug':
      return 'background-color: limegreen;';
    case 'rock':
      return 'background-color: goldenrod;';
    default:
      return 'background-color: grey;';
  }
}

function getTypeStyle(type) {
    switch (type.toLowerCase()) {
      case 'fire':
      return 'background-color: red;';
    case 'water':
      return 'background-color: blue;';
    case 'grass':
      return 'background-color: green;';
    case 'fighting':
      return 'background-color: orange;';
    case 'electric':
      return 'background-color: yellow;';
    case 'ice':
      return 'background-color: lightblue;';
    case 'poison':
      return 'background-color: pink;';
    case 'flying':
      return 'background-color: skyblue;';
    case 'psychic':
      return 'background-color: purple;';
    case 'bug':
      return 'background-color: limegreen;';
    case 'rock':
      return 'background-color: goldenrod;';
    default:
      return 'background-color: grey;';
    }
  }

function calculateBarWidth(stat) {
  const maxStat = 255;
  const percentage = (stat / maxStat) * 100;
  return `${percentage}%`;
}

async function getWeaknesses(types) {
  let weaknesses = new Set();
  for (let type of types) {
    let url = `https://pokeapi.co/api/v2/type/${type.type.name}`;
    let { data } = await axios.get(url);
    data.damage_relations.double_damage_from.forEach((weakness) => {
      weaknesses.add(weakness.name);
    });
  }
  return Array.from(weaknesses);
}



const typePokemon1Style = computed(() => getBackgroundColor(tipoPokemon1.value));
const typePokemon2Style = computed(() => getBackgroundColor(tipoPokemon2.value));
const columna1Style = computed(() => getBackgroundColor(tipoPokemon1.value));

</script>






<style>
* {
  margin: 0;
  font-family: 'Gill Sans', 'Gill Sans MT', Calibri, 'Trebuchet MS', sans-serif;
}

.title {
  display: grid;
  grid-template-columns: 1fr 1fr 1fr;
  justify-content: center;
  margin: 15px;
}

.contenido {
  display: grid;
  grid-template-columns: 1fr 1fr 1fr;
  justify-content: center;
}

.titlePrincipal {
  width: 400px;
}

.imgPokemon {
  display: grid;
  justify-items: center;
}

.imgTitle {
  width: 100px;
}

.tercerImg {
  display: grid;
  justify-items: end;
}

.columna1 {
  background-color: red;
  margin: 20px;
  width: 100%;
  height: 500px;
  border-radius: 10px;
}


.columna1 h1 {
  text-align: center;
  margin: 20px;
}

.columna1 img {
  display: block;
  margin: auto;
  width: 300px;
  height: 300px;
}

.pesoAltura {
  display: flex;
  justify-content: center;
  margin: 30px;
}

.pesoAltura h2 {
  margin: 20px;
}

.columna2 {
  margin: 20px;
}

.columna2 p {
  font-size: 80px;
  text-align: center;
  margin-top: 50px;
}

.columna2 h1 {
  text-align: center;
  margin-top: 30px;
}

.types {
  display: flex;
  justify-content: center;
  margin-top: 23px;
}

#typePokemon{
  width: 80px;
  height: 30px;
  border-radius: 10px;
  text-align: center;
}

.weaknesses {
  display: flex;
  flex-wrap: wrap;
  justify-content: center;
  margin-top: 20px;
}

.weakness {
  padding: 5px 10px;
  margin: 5px;
  border-radius: 5px;
}

.columna3 {
  margin: 30px;
  background-color: gainsboro;
  padding: 30px;
  border-radius: 10px;
}

.stats {
  margin-top: 20px;
}

.stat {
  display: flex;
  align-items: center;
  margin-bottom: 10px;
}

.stat span {
  width: 100px;
}

.bar {
  height: 20px;
  background-color: #4caf50;
}

.columna4 {
  display: grid;
  justify-content: center;
  margin: 20px;
}

.columna4 input.pokemon-input {
  padding: 10px;
  border-radius: 10px;
  border: 1px solid #ccc;
  margin-bottom: 10px;
  width: 200px;
}

.columna4 button {
  margin: 10px;
  padding: 10px;
  border-radius: 10px;
  background-color: #f1c40f;
  color: white;
  border: none;
  cursor: pointer;
}

.columna4 button:hover {
  background-color: #d4ac0d
}

@media (max-width: 950px) {
  .contenido {
    display: grid;
    grid-template-columns: 1fr;
    grid-template-rows: repeat(3, 1fr);
  }
}

@media (max-width: 620px) {
  #imgSmall {
    display: none;
  }

  .columna1 {
    width: 400px;
  }
}
</style>
