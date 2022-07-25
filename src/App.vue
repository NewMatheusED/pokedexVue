<template>
  <div class="container">
    <div class="loading hide"></div>
    <div class="main">
      <input class="form-control input" type="text" placeholder="Id ou nome" v-model="pokemon">
      <img class='mainImg'/>
      <h1 style="text-transform: capitalize;">Nome: {{pokemonData['name']}}</h1>
      <h2>Id: {{pokemonData['id']}}</h2>
      <button class="btn btn-primary" @click="getApiData">Search</button>
      <button class="btn btn-secondary hide" style="margin-top: 10px" @click="__repeat">Pesquisar novamente</button>
    </div><!--main-->
  </div><!--container-->
</template>

<script>
  export default {
    name: "App",
    data() {
      return {
        pokemonData: [],
        pokemon: ''
        }
      },  
 
      methods: {
        async getApiData() {
          const div1 = document.querySelector('.loading');
          const div2 = document.querySelector('.main');
          const img = document.querySelector('.mainImg');
          const input = document.querySelector('.input');
          const btn1 = document.querySelector('.btn-primary');
          const btn2 = document.querySelector('.btn-secondary');
          this.pokemonData = [];
          div1.classList.add("show");
          div1.classList.remove("hide");
          div2.classList.add("hide");
          div2.classList.remove("show");
          btn1.classList.add("hide");
          btn1.classList.remove("show");
          btn2.classList.add("show");
          btn2.classList.remove("hide");
          try {
            if(this.pokemon === '') {
              alert('Preencher campo')
              window.location.reload();
            }
            let name = this.pokemon.toLowerCase();
            const res = await fetch(`https://pokeapi.co/api/v2/pokemon/${name}`);
            if(res.status == 200) { //se o pokemon existe
              const data = await res.json();
              this.pokemonData = data ;
              this.pokemon = '';
              img.src = data['sprites']['versions']['generation-v']['black-white']['front_default'];
              input.classList.add("hide");
              if (this.pokemonData.length != 0) { 
                div1.classList.add("hide");
                div1.classList.remove("show");
                div2.classList.add("show");
                div2.classList.remove("hide");
              }
            } else if (res.status == 404) { //se o pokemon não existe
              this.pokemonData.name = 'Pokemon não encontrado';
              this.pokemonData.id = 'Error 404';
              img.src = '';
              div1.classList.add("hide");
              div1.classList.remove("show");
              div2.classList.add("show");
              div2.classList.remove("hide");
              this.pokemon = '';
            }

          }
          catch(error) {
            console.log(error);
          }
        },

        __repeat() {
          window.location.reload();
        }
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
    display: flex;
    flex-direction: column;
    align-items: center;
   }

   @keyframes spin {
    0% {
      transform:translate(-50%, -50%) rotate(0deg); 
    }
    100% {
      transform: translate(-50%, -50%) rotate(360deg) ;
    }
   }
</style>