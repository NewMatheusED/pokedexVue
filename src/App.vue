<template>
  <div class="container">
    <div v-bind:class = "(this.pokemonData.length == 0) ? 'hide' : 'show'" class="loading"></div>
    <div class="main">
      <input class="form-control input" type="text" placeholder="Id ou nome" v-model="pokemon">
      <button class="btn btn-primary" @click="getApiData">Search</button>
      <div class="flexInfo">
        <h1 class="title" style="text-transform: capitalize;">{{pokemonData['name']}}&nbsp;</h1>
        <h2 v-if="pokemonData['id'] < 100" class="title">Nº0{{pokemonData['id']}}</h2>
        <h2 v-if="pokemonData['id'] > 100" class="title">Nº{{pokemonData['id']}}</h2>
      </div>
      <img class='mainImg'/>
      <!-- <p>{{pokemonDataDescriptions.descriptions[7]}}</p> -->
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
        pokemonDataDescriptions: [],
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
          // const title = document.querySelectorAll('.title');

          this.pokemonData = [];

          // div1.classList.add("show");
          // div1.classList.remove("hide");
          // div2.classList.add("hide");
          // div2.classList.remove("show");
          // btn1.classList.add("hide");
          // btn1.classList.remove("show");

          try {
            if(this.pokemon === '') {
              alert('Preencher campo')
              window.location.reload();
            }
            let name = this.pokemon.toLowerCase();
            const res = await fetch(`https://pokeapi.co/api/v2/pokemon/${name}`);
            const resDescription = await fetch(`https://pokeapi.co/api/v2/characteristic/${name}`)

            if(res.status == 200) { //se o pokemon existe
              const data = await res.json();
              const description = await resDescription.json();
              this.pokemonData = data ;
              this.pokemon = '';
              this.pokemonDataDescriptions = description;
              img.src = data['sprites']['versions']['generation-v']['black-white']['animated']['front_default'];
              input.classList.add("hide");
              if (this.pokemonData.length != 0) { 
                div1.classList.add("hide");
                div1.classList.remove("show");
                div2.classList.add("show");
                div2.classList.remove("hide");
                btn2.classList.add("show");
                btn2.classList.remove("hide");
                // title.forEach(el => {
                //   el.classList.add("show");
                //   el.classList.remove("hide");
                // });
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