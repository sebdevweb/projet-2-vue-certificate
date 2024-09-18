<script setup>
  import { ref } from 'vue' // Importation de 'ref' de Vue pour créer des références réactives.
  import { StarIcon } from '@heroicons/vue/24/solid' // Importation de l'icône de star depuis Heroicons pour afficher les étoiles.
  import { items } from './movies.json' // Importation d'un fichier JSON local qui contient une liste de films (données fictives).
  
  const text = ref('MOVIE RATING APP') // Déclare une variable réactive 'text' qui contient le titre de l'application.
  const movies = ref(items) // Déclare une variable réactive 'movies' qui contient les films importés du fichier JSON.

  const starActive = ref({color: 'rgb(213, 180, 16)'}) // Déclare une variable réactive sous forme d'objet qui contient une valeur de style css
  const starDeactive = ref({color: 'grey'}) // Déclare une variable réactive sous forme d'objet qui contient une valeur de style css

  // Fonction qui met à jour la note d'un film particulier
  function updateRating(movieIndex, rating) { // Cette fonction prend deux paramètres : movieIndex (l'index du film dans la liste) et rating (la nouvelle note).
    movies.value[movieIndex].rating = rating // Accède à la liste des films réactifs, trouve le film correspondant à l'index donné, et met à jour sa note avec la nouvelle valeur.
  }
</script>


<template>
  <header>
    <h1>{{ text }}</h1> <!-- Affiche le titre de l'application défini dans la variable 'text'. -->
  
    <div class="new_movie">
      <button class="add" type="button">Add Movie</button>
    </div>
  </header>
  
  <div class="movies">
    <!-- Card Movie -->
     <!-- Boucle pour afficher chaque film sous forme de carte. -->
    <div class="card" v-for="(movie, movieIndex) in movies" :key="movie.id">
      <div class="card_pic">
        <img :src="movie.image" :alt="movie.name">  <!-- Affiche l'image du film, la source de l'image et son alt sont dynamiques. -->
        <div class="card_bigstar">
          <StarIcon :style="movie.rating >= 1 ? starActive : starDeactive" /> <!-- Si la note du film est supérieur ou égal à 1 alors affiche le style color starActive sinon affiche starDeactive  -->
          <span v-if="movie.rating <= 0">-</span> <!-- Si la note du film est inférieur ou égal à 0 : affiche un tiret à la place d'un nombre-->
          <span v-else>{{ movie.rating }}</span><!-- Sinon affiche la note du film -->
        </div>
      </div>
      <div class="card_info">
        <h2 class="card_info--title">{{ movie.name }}</h2> <!-- Titre du film. -->

        <!-- Liste des genres du film. Chaque genre est affiché dans un paragraphe distinct. -->
        <div class="card_info--captions">
          <p class="caption" v-for="genre in movie.genres">{{ genre }}</p> 
        </div>

        <!-- Affiche la description du film. -->
        <p class="card_info--description">{{ movie.description }}</p>

        <!-- Liste des acteurs du film. Chaque acteur est affiché dans un paragraphe distinct. -->
        <div class="card_info--actors">
          <p class="actor" v-for="actor in movie.actors">{{ actor }}</p>
        </div>

        <!-- Section pour afficher et mettre à jour la note (rating) du film. -->
        <div class="card_info--rating">
          <p>Rating [{{ movie.rating }}/5]</p> <!-- Affiche la note actuelle du film. -->

          <!-- Section étoiles pour permettre la notation par l'utilisateur. -->
          <div class="stars">

            <!-- Boucle qui affiche 5 étoiles. -->
            <!-- type : Définit le type du bouton comme étant un bouton simple. -->
            <!-- v-for : Crée une boucle qui génère 5 boutons, correspondant aux 5 étoiles. 'star' prend les valeurs de 1 à 5. -->
            <!-- :key : Assigne une clé unique à chaque bouton en utilisant la valeur de 'star'. Cela permet à Vue d'identifier chaque étoile de manière unique. -->
            <!-- :class : Applique une classe conditionnelle : 'gold' si 'star' est inférieur ou égal à la note actuelle du film (cela rend l'étoile dorée), sinon 'grey' (étoile grisée). -->
            <!-- @click : Attache l'événement 'click' qui appelle la fonction 'updateRating'. L'utilisateur clique sur une étoile et envoie l'index du film et la valeur de 'star' (1 à 5), qui met à jour la note du film. -->
            <!-- :disabled : Désactive le bouton si la valeur de 'star' correspond déjà à la note actuelle du film, empêchant ainsi l'utilisateur de sélectionner la même note deux fois de suite. -->
            <button 
              type="button" 
              v-for="star in 5" 
              :key="star"
              :class="star <= movie.rating ? 'gold' : 'grey'"
              @click="updateRating(movieIndex, star)"
              :disabled="star === movie.rating"
              >
              <StarIcon /> <!-- Affiche l'icône de l'étoile, depuis le composant importé StarIcon -->
            </button>
          </div>
        </div>
      </div>
    </div>
  </div>
  
</template>


<style lang="scss">
  body {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
    font-family: sans-serif;
  }

  header {
    display: flex;
    height: 150px;
    position: relative;
    align-items: center;

    h1 {
      text-align: center;
      color: #5A94E2;
      border: #5A94E2 3px dashed;
      max-width: 400px;
      margin: 24px auto;
      padding: 12px;
      height: 30px;
    }
    .new_movie {
      position: absolute;
      right: 20px;
      top: 50%;
      transform: translateY(-50%);
      .add {
        border: none;
        padding: 10px 16px;
        font-size: 18px;
        border-radius: 10px;
        background: #5A94E2;
        color: #fff;
        font-weight: 400;
        cursor: pointer;
        transition: .3s ease-out;
        &:hover {
          background: #42b683;
          transition: .2s ease-in;
        }
      }
    }
  }
  


  .movies {
    display: flex;
    gap: 16px;
    justify-content: space-around;
    background-color: #42b683;
    padding: 48px;
    height: auto;
    flex-wrap: wrap;
    max-width: 1600px;
    margin: 0 auto;
    border-radius: 20px;
    margin-bottom: 48px;

    .card {
      max-width: 400px;
      height: auto;
      border-radius: 10px;
      overflow: hidden;
      box-shadow: 0px 0px 30px rgba(0, 0, 0, 0.3);
      &_pic {
        width: 400px;
        height: 530px;
        overflow: hidden;
        position: relative;
        img {
          max-width: 400px;
          height: auto;
        }
      }
      &_bigstar {
        position: absolute;
        top: 10px;
        right: 0;
        svg {
          width: 80px;
          color: rgb(213, 180, 16);
        }
        span {
          position: absolute;
          z-index: 4;
          transform: translate(0%, -50%);
          right: 44%;
          top: 50%;
          font-size: 24px;
          font-weight: bold;
        }
      }
      &_info {
        padding: 20px;
        height: 280px;
        background-color: #fff;
        position: relative;
        &--title {
          margin: 0 0 8px 0;
          line-height: 1;
          font-size: 22px;
        }
        &--captions {
          display: flex;
          gap: 8px;
          .caption {
            background-color: #5A94E2;
            padding: 4px 8px;
            border-radius: 5px;
            color: #fff;
            margin-top: 0;
          }
        }
        &--description {
          color: #333;
          line-height: 1.3;
          margin-top: 8px;
        }
        &--actors {
          display: flex;
          flex-wrap: wrap;
          word-break: normal;
          word-spacing: -2px;
          gap: 10px;
          .actor {
            color: #5A94E2;
            line-height: .5;
            margin: 0;
          }
        }
        &--rating {
          display: flex;
          align-items: center;
          justify-content: space-between;
          position: absolute;
          left: 20px;
          bottom: 0;
          .stars {
            margin-left: 10px;
            gap: 5px;
            button {
              cursor: pointer;
              border: none;
              background: none;
              padding: 2px;
              &.gold {
                color: rgb(213, 180, 16);
              }
              &.grey {
                color: lightgray;
              }
              svg {
                width: 20px;
              }
              &:disabled {
                cursor: not-allowed;
              }
            }
          }
        }
      }
    }
  }
</style>
