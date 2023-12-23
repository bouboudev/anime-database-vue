<template>
  <div class="app">
    <header>
      <h1>La <strong>Base de données </strong>des Anime</h1>
      <form class="search-box" @submit.prevent="HandleSearch">
        <input
          type="search"
          class="search-field"
          placeholder="Entrez un Anime puis tapez sur la touche Entrée"
          required
          v-model="search_query"
        />
      </form>
    </header>

    <main>
      <div class="cards" v-if="animelist && animelist.length > 0">
        <Card v-for="anime in animelist" :key="anime.mal_id" :anime="anime" />
      </div>
      <div v-else-if="animelist.length < 0 || !isLoading">
        {{message}}
      </div>
      <div class="loading" v-if='isLoading'>
        <Loading/>
      </div>
    </main>
  </div>
</template>

<script>
import { ref, onMounted } from "vue";
import Card from "./components/Card";
import Loading from "./components/Loading";

export default {
  components: {
    Card,
    Loading,
  },
  setup() {
    const search_query = ref("");
    const animelist = ref([]);
    const message = ref("Aucun résultat trouvé");
    const isLoading = ref(false);

    const HandleSearch = async () => {
      isLoading.value = true;
      animelist.value = [];
      animelist.value = await fetch(
        `https://api.jikan.moe/v4/anime?q=${search_query.value}`
      )
        .then((res) => res.json())
        .then((data) => data.data)
        .catch((err) => console.log(err));

      search_query.value = "";
      isLoading.value = false;
    };

    const HandleTopSearch = async () => {
      isLoading.value = true;
      animelist.value = await fetch('https://api.jikan.moe/v4/top/anime')
        .then((res) => res.json())
        .then((data) => data.data)
        .catch((err) => console.log(err));

      search_query.value = "";
      isLoading.value = false;
    };

    onMounted(() => {
      HandleTopSearch();
    });

    return {
      Card,
      search_query,
      animelist,
      HandleSearch,
      HandleTopSearch,
      isLoading,
      message
    };
  },
};
</script>


<style lang="scss">
@import url("https://fonts.googleapis.com/css2?family=Fira+Sans:wght@500&display=swap");

* {
  box-sizing: border-box;
  margin: 0;
  padding: 0;
  font-family: "Fira Sans", sans-serif;
}

:root {
  --couleur-secondaire: #bf1725;
  --couleur-principale: #595959;
}

a {
  text-decoration: none;
}
header {
  padding-top: 50px;
  padding-bottom: 50px;
  h1 {
    color: var(--couleur-principale);
    font-size: 42px;
    font-weight: 400;
    text-align: center;
    text-transform: uppercase;
    margin-bottom: 30px;
    strong {
      color: var(--couleur-secondaire);
    }
    &:hover {
      color: var(--couleur-secondaire);
    }
    &:hover strong {
      color: var(--couleur-principale);
    }

  }
  .search-box {
    display: flex;
    justify-content: center;
    padding-left: 30px;
    padding-right: 30px;
    .search-field {
      appearance: none;
      background: none;
      border: none;
      outline: none;
      background-color: #f3f3f3;
      box-shadow: 0px 4px 8px rgba(0, 0, 0, 0.15);
      display: block;
      width: 100%;
      max-width: 600px;
      padding: 15px;
      border-radius: 8px;
      color: var(--couleur-secondaire);
      font-size: 20px;
      transition: 0.4s;
      &::placeholder {
        color: #000000;
      }
      &:focus,
      &:valid {
        color: #fff;
        background-color: var(--couleur-secondaire);
        box-shadow: 0px 0px 0px rgba(0, 0, 0, 0.15);
      }
    }
  }
}
main {
  max-width: 1200px;
  margin: 0 auto;
  padding-left: 30px;
  padding-right: 30px;
  .cards {
    display: flex;
    flex-wrap: wrap;
    margin: 0 -8px;
  }
}
.loading {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 100vh;
  h2 {
    color: var(--couleur-secondaire);
    font-size: 32px;
    font-weight: 400;
    text-align: center;
    text-transform: uppercase;
  }
}
</style>
