<template>
  <div class="container">
    <div class="loading hide"></div>
    <div class="main">
      <div v-bind:class="(pokemonData.length == 0) ? 'show' : 'hide'" style="width: 100%; text-align: center">
        <input class="form-control input" type="text" placeholder="Id ou nome" v-model="pokemon">
        <button class="btn btn-primary" @click="getApiData">Search</button>
      </div><!--first-->
      <div style="margin-bottom: -30px" class="flexInfo">
        <h1 class="title" style="text-transform: capitalize;">{{pokemonData['name']}}&nbsp;</h1>
        <h2 v-if="pokemonData['id'] < 100" class="title">Nº0{{pokemonData['id']}}</h2>
        <h2 v-if="pokemonData['id'] >= 100" class="title">Nº{{pokemonData['id']}}</h2>
      </div><!--flexInfo-->
      <div class="flexInfo">
        <button v-bind:class="(pokemonData.length == 0) ? 'hide' : 'show'" class="btn btn-secondary" @click="previous">Anterior</button>
        <button v-bind:class="(pokemonData.length == 0) ? 'hide' : 'show'" class="btn btn-secondary" @click="foward">Próximo</button>
      </div><!--flexInfo-->
      <img class='mainImg'/>
      <div class="typesInfo"></div>
      <div class="description"></div>
      <h1 v-bind:class="(pokemonData.length == 0) ? 'hide' : 'show'" >Evolutions</h1>
      <div class="flexEvolution">
        <div class="evolution" v-for="(evolve, index) in evolutions" :key="index">
            <img :src="getSpriteUrl(evolve.id)" @error="handleImageError" class="evolutionImg">
            <h3 class="evolutionName">{{ evolve.name }}</h3>
            <p class="evolutionId">Nº{{ evolve.id }}</p>
        </div><!--evolution-->
      </div><!--flexEvolution-->
      
      <button v-bind:class="(pokemonData.length == 0) ? 'hide' : 'show'" class="btn btn-secondary" style="margin-top: 10px; margin-bottom: 20px" @click="__repeat">Pesquisar novamente</button>
    </div><!--main-->
  </div><!--container-->
</template>

<script>
  export default {
    name: "App",
    data() {
      return {
        pokemonData: [],
        pokemon: '',
        id: 0,
        evolutions: []
        }
      },  
 
      methods: {

        async getApiData() {
          const loading = document.querySelector('.loading');
          const img = document.querySelector('.mainImg');
          const input = document.querySelector('.input');
          const typesInfo = document.querySelector('.typesInfo');
          const description = document.querySelector('.description');

          loading.classList.add("show");
          loading.classList.remove("hide");

          try {

            if(this.pokemon === '') {
              alert('Preencher campo')
              window.location.reload();
            }

            let name = this.pokemon
            const res = await fetch(`https://pokeapi.co/api/v2/pokemon/${name}`);
            const resEvol = await fetch(`https://pokeapi.co/api/v2/pokemon-species/${name}/`)

            if(res.status == 200) { //se o pokemon existe
              const data = await res.json();
              const result = await resEvol.json()

              this.pokemonData = data ;
              this.pokemon = '';
              this.id = this.pokemonData['id'];

              if(data['sprites']['versions']['generation-v']['black-white']['animated']['front_default'] != null) {
                img.src = data['sprites']['versions']['generation-v']['black-white']['animated']['front_default'];
              }else{
                img.src = data['sprites']['other']['official-artwork']['front_default'];
              }

              input.classList.add("hide");

              description.innerHTML = `HP: ${this.pokemonData['stats'][0]['base_stat']} <br /> Attack: ${this.pokemonData['stats'][1]['base_stat']} <br /> Defense: ${this.pokemonData['stats'][2]['base_stat']} <br /> Special Attack: ${this.pokemonData['stats'][3]['base_stat']} <br /> Special Defense: ${this.pokemonData['stats'][4]['base_stat']} <br /> Speed: ${this.pokemonData['stats'][5]['base_stat']}`

              if(this.pokemonData['types'].length == 1) {
                document.createElement("loading")
                let type1 = this.pokemonData['types'][0]['type']['name']
                typesInfo.innerHTML = type1;
                loading.classList.add("hide");
                loading.classList.remove("show");
                
              }

              if(this.pokemonData['types'].length == 2) {
                let type1 = this.pokemonData['types'][0]['type']['name']
                let type2 = this.pokemonData['types'][1]['type']['name']
                typesInfo.innerHTML = type1 + " | " + type2;
                loading.classList.add("hide");
                loading.classList.remove("show");
              }
              // if (this.pokemonData.length != 0) { 
                // div1.classList.add("hide");
                // div1.classList.remove("show");
                // title.forEach(el => {
                //   el.classList.add("show");
                //   el.classList.remove("hide");
                // });
              // }

              if (result["evolution_chain"] !== null) {
                const evol = await fetch(result["evolution_chain"]["url"]);
                const evolutionChainData = await evol.json();

                this.evolutions = [];
                this.collectEvolutions(evolutionChainData["chain"]);
              }

            } else if (res.status == 404) { //se o pokemon não existe
              // this.pokemonData.name = 'Pokemon não encontrado';
              // this.pokemonData.id = 'Error 404';
              // img.src = '';
              // div1.classList.add("hide");
              // div1.classList.remove("show");
              // div2.classList.add("show");
              // div2.classList.remove("hide");
              // this.pokemon = '';
              alert("Pokemon não encontrado")
              window.location.reload();
            }

          }
          catch(error) {
            console.log(error);
          }
        },

        __repeat() {
          window.location.reload();
        },

        foward() {
          if(this.id == 905) {
            this.id = 0
          }else{
            this.id++
            this.pokemon = this.id;
            document.querySelector('.mainImg').src = ''
            document.querySelector('.typesInfo').innerHTML = ''
            this.getApiData();
          }
        },

        previous() {
          if(this.id == 1) {
            this.id = 906
          }else{
            this.id--
            this.pokemon = this.id;
            document.querySelector('.mainImg').src = ''
            document.querySelector('.typesInfo').innerHTML = ''
            this.getApiData();
          }
        },

        collectEvolutions(chain) {
          const currentEvolution = {
            name: chain["species"]["name"],
            id: chain["species"]["url"].split("/").slice(-2, -1)[0],
          };
          this.evolutions.push(currentEvolution);

          if (chain["evolves_to"].length > 0) {
            for (const evolutionData of chain["evolves_to"]) {
              this.collectEvolutions(evolutionData);
            }
          }
        },

        getSpriteUrl(pokemonName) {
          // Gerar a URL do gif pixelado do Pokémon
          return `https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/versions/generation-v/black-white/animated/${pokemonName}.gif`;
        },

        // async setEvolve(el) {
        //   console.log(evolutions)
        //   try{
        //     const res = await fetch(`https://pokeapi.co/api/v2/pokemon/${el}`);
        //     const evolve = await res.json();
        //     return evolve;
        //   }
        //    catch(error) {
        //     console.log(error)
        //    }   
        // },

        // setEvolutions() {
        //   return this.evolutions.map(el => {
        //     setEvolve(el)
        //   })
        // }

      },

      creates() {
        this.getApiData()
      }
  }
</script>

<style>
   .show {
    display: inline-block;
   }

    .hide {
    display: none;
    }

   .input {
    margin-top: 20px;
    margin-bottom: -20px;
   }

   .btn {
    margin-top: 40px;
    margin-bottom: -40px;
   }

   .loading {
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    width: 100px;
    height: 100px;
    border-radius: 50%;
    background-color: #fff;
    border: 2px solid #000;
    border-left-color: #fff;
    animation: spin 1s linear infinite;
   }

   .main {
    background-color: rgba(180, 180, 180, .2);
    padding: 0 10px;
    border-radius: 10px;
    display: flex;
    flex-direction: column;
    align-items: center;
   }

   .mainImg {
    width: 150px;
    /* border: 2px solid rgba(150, 150, 150, .3); */
    border-radius: 10px;
    padding: 10px;
   }

   .flexInfo {
    display: flex;
    align-items: end;
    margin-bottom: 50px;
   }

   .flexEvolution {
    display: flex;
   }

   .evolution {
    text-align: center;
    margin: 0 50px;
   }

   .evolutionImg {
    width: 80px;
    height: 80px;
   }
   /* .evolution {

   } */

   @keyframes spin {
    0% {
      transform:translate(-50%, -50%) rotate(0deg); 
    }
    100% {
      transform: translate(-50%, -50%) rotate(360deg) ;
    }
   }
</style>