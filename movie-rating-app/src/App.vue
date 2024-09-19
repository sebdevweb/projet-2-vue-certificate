<script setup>
  import { ref, reactive, computed } from 'vue' // Importation de 'ref' de Vue pour créer des références réactives.
  import { StarIcon, PencilIcon, TrashIcon } from '@heroicons/vue/24/solid' // Importation de l'icône de star depuis Heroicons pour afficher les étoiles.
  import { items } from './movies.json' // Importation d'un fichier JSON local qui contient une liste de films (données fictives).
  
  const text = ref('MOVIE RATING APP') // Déclare une variable réactive 'text' qui contient le titre de l'application.
  const movies = ref(items) // Déclare une variable réactive 'movies' qui contient les films importés du fichier JSON.
  const starActive = ref({color: 'rgb(213, 180, 16)'}) // Déclare une variable réactive sous forme d'objet qui contient une valeur de style css
  const starDeactive = ref({color: 'grey'}) // Déclare une variable réactive sous forme d'objet qui contient une valeur de style css

  // Propriété calculé computed pour déterminer le nombre d'items total
  const totalMovies = computed(() => {
    return movies.value.length
  })

  // Fonction qui met à jour la note d'un film particulier
  function updateRating(movieIndex, rating) { // Cette fonction prend deux paramètres : movieIndex (l'index du film dans la liste) et rating (la nouvelle note).
    movies.value[movieIndex].rating = rating // Accède à la liste des films réactifs, trouve le film correspondant à l'index donné, et met à jour sa note avec la nouvelle valeur.
  }

  // Supprime un film de la liste en filtrant le tableau movies selon l'indice du film à supprimer.
  function removeMovie(movieIndex) {
    movies.value = movies.value.filter((movie, index) => index !== movieIndex);
  }

  // Pré-remplit un formulaire avec les informations du film sélectionné pour permettre son édition.
  function editMovie(movieIndex) {
    const movie = movies.value[movieIndex];

    form.id = movie.id;
    form.name = movie.name;
    form.description = movie.description;
    form.image = movie.image;
    form.inTheaters = movie.inTheaters;
    form.genres = movie.genres;

    showForm();
  }

  // Cet objet suit les erreurs de validation pour chaque champ du formulaire (nom, description, image, etc.).
  const errors = reactive({
    name: null,
    description: null,
    image: null,
    inTheaters: null,
    genres: null,
  });

  // Contient les données du formulaire utilisé pour ajouter ou éditer un film.
  const form = reactive({
    id: null,
    name: null,
    description: null,
    image: null,
    inTheaters: false,
    genres: null,
  });

  // Définit les règles de validation, ici les champs name et genres sont requis.
  const validations = reactive({
    name: "required",
    genres: "required",
  });

  // Gestion de l'affichage des options dans le select
  const genres = reactive([
    { text: "Drama", value: "Drama" },
    { text: "Crime", value: "Crime" },
    { text: "Action", value: "Action" },
    { text: "Comedy", value: "Comedy" },
  ]);

  // Retourne une expression régulière utilisée pour valider les champs requis.
  const validationRules = (rule) => {
    if (rule === "required") return /^ *$/;

    return null;
  };

  // Vérifie si les champs du formulaire respectent les règles de validation. Si une erreur est détectée, elle est enregistrée dans errors.
  function validate() {
    let valid = true;
    clearErrors();
    for (const [field, rule] of Object.entries(validations)) {
      const validation = validationRules(rule);
      if (validation) {
        if (validation.test(form[field] || "")) {
          errors[field] = `${field} is ${rule}`;
          valid = false;
        }
      }
    }

    return valid;
  }

  // Sauvegarde un film, soit en le mettant à jour si form.id existe, soit en en ajoutant un nouveau.
  function saveMovie() {
    if (form.id) {
      updateMovie();
    } else {
      addMovie();
    }
  }

  // Met à jour un film existant en validant le formulaire et en remplaçant le film dans la liste.
  function updateMovie() {
    if (validate()) {
      const movie = {
        id: form.id,
        name: form.name,
        description: form.description,
        image: form.image,
        genres: form.genres,
        inTheaters: form.inTheaters,
        rating: 0,
      };

      movies.value = movies.value.map((m) => {
        if (m.id === movie.id) {
          movie.rating = m.rating;
          return movie;
        }
        return m;
      });

      hideForm();
    }
  }

  // Ajoute un nouveau film après validation.
  function addMovie() {
    if (validate()) {
      const movie = {
        id: Number(Date.now()),
        name: form.name,
        description: form.description,
        image: form.image,
        genres: form.genres,
        inTheaters: form.inTheaters,
        rating: 0,
      };
      movies.value.push(movie);
      hideForm();
    }
  }

  // Réinitialisent le formulaire et les erreurs après soumission ou annulation.
  function cleanUpForm() {
    form.name = null;
    form.description = null;
    form.image = null;
    form.genres = null;
    form.id = null;
    form.inTheaters = false;
    clearErrors();
  }
  function clearErrors() {
    errors.name = null;
    errors.description = null;
    errors.image = null;
    errors.genres = null;
    errors.inTheaters = null;
  }

  const showMovieForm = ref(false) // Variable réactive qui détermine si le formulaire est visible ou non
  function hideForm() {
    showMovieForm.value = false;
    cleanUpForm()
  }
  function showForm() {
    showMovieForm.value = true;
  }

  
</script>


<template>
  <!-- MODAL FORM -->
  <div class="modal" v-if="showMovieForm">
    <div class="modal_content">
      <h2>Add new movie</h2>
      <form @submit.prevent="saveMovie">
        <fieldset>
          <label for="name">Name</label>
          <input type="text" id="name" placeholder="Add movie name" v-model="form.name">
          <span class="error">{{ errors.name }}</span>
        </fieldset>
        <fieldset>
          <label for="description">Description</label>
          <textarea id="description" placeholder="Add description movie" v-model="form.description"></textarea>
        </fieldset>
        <fieldset>
          <label for="image">Image</label>
          <input type="text" id="image" placeholder="Add movie image url only!" v-model="form.image">
        </fieldset>
        <fieldset>
          <label for="genre">Genre <span>(you can select further options)</span></label>
          <select name="genre" v-model="form.genres" multiple>
            <option v-for="option in genres" :key="option.value" :value="option.value">
              {{ option.text }}
            </option>
          </select>
          <span class="error">{{ errors.genres }}</span>
        </fieldset>
        <fieldset>
          <label for="theatre">In Theatre</label>
          <input type="checkbox" id="theatre" v-model="form.inTheaters">
          <span class="error">{{ errors.inTheaters }}</span>
        </fieldset>
        <fieldset class="actions">
          <button type="button" class="cancel" @click="hideForm">Cancel</button>
          <button type="submit" class="create">
            <span v-if="form.id">Update</span>
            <span v-else>Create</span>
          </button>
        </fieldset>
      </form>
    </div>
  </div>

  <header :class="showMovieForm == true ? 'blur' : ''">
    <h1>{{ text }}</h1> <!-- Affiche le titre de l'application défini dans la variable 'text'. -->
  
    <div class="header_actions">
      <div class="total">
        Total Movie{{ totalMovies > 1 ? 's' : '' }}: <span>{{ totalMovies }}</span>
      </div>
      <button 
        class="add" 
        type="button" 
        @click="showForm"
      >
      Add Movie
    </button>
    </div>
  </header>
  
  <div 
    class="movies" 
    :class="showMovieForm == true ? 'blur' : ''"
    :style="totalMovies < 1 ? 'background-color: unset' : ''"
    >
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

          <div class="action">
            <button
              class="edit"
              @click="editMovie(movieIndex)"
            >
              <PencilIcon />
            </button>
            <button
              class="remove"
              @click="removeMovie(movieIndex)"
            >
              <TrashIcon />
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

  .modal {
    display: flex;
    justify-content: center;
    flex-direction: column;
    align-items: center;
    height: 100vh;
    width: 100vw;
    position: fixed;
    top: 0;
    left: 0;
    background: rgba(0, 0, 0, .7);
    z-index: 2;
    &_content {
      background: #fff;
      padding: 48px;
      max-width: 800px;
      width: 100%;
      border-radius: 20px;
      box-shadow: 0px 0px 30px rgba(0, 0, 0, 0.5);
      h2 {
        text-align: center;
        color: #5A94E2;
        font-size: 32px;
        margin-top: 0;
      }
      form {
        fieldset {
          border: none;
          display: flex;
          flex-direction: column;
          label {
            font-size: 16px;
            color: #333;
            margin-bottom: 10px;
            span {
              font-style: italic;
            }
          }
          input, textarea, select {
            width: 100%;
            max-width: 800px;
            border: 1px solid #999;
            padding: 10px;
            background: #5a95e21c;
            font-size: 16px;
            font-family: sans-serif;
            overflow-y: auto;
            color: #333;
            &::placeholder {
              font-size: 14px;
              color: #999;
              font-style: italic;
              font-family: sans-serif;
            }
            
          }
          &:nth-child(5) {
            align-items: center;
            flex-direction: row;
            label {
              margin-bottom: 0;
            }
            input[type="checkbox"] {
              width: 20px;
            }
          }
          .error {
            color: crimson;
            font-style: italic;
            margin-top: 5px;
          }
          &.actions {
            display: flex;
            flex-direction: row;
            justify-content: space-between;
            margin-top: 30px;
            button {
              border: none;
              padding: 10px 20px;
              border-radius: 7px;
              font-size: 18px;
              color: #fff;
              cursor: pointer;
              transition: .3s ease-out;
              &.cancel {
                background: #999;
                transition: .2s ease-in;
                &:hover {
                  background: #666;
                }
              }
              &.create {
                background: #5A94E2;
                &:hover {
                  background: #42b683;
                  transition: .2s ease-in;
                }
              }
            }

          }
        }
      }
    }
  }

  header {
    display: flex;
    height: 150px;
    position: relative;
    align-items: center;
    &.blur {
      filter:blur(4px)
    }

    h1 {
      text-align: center;
      color: #5A94E2;
      border: #5A94E2 3px dashed;
      max-width: 400px;
      margin: 24px auto;
      padding: 12px;
      height: 30px;
    }
    .header_actions {
      position: absolute;
      right: 20px;
      top: 50%;
      transform: translateY(-50%);
      display: flex;
      align-items: center;
      gap: 24px;
      .total {
        color: #666;
        font-style: italic;
        span {
          font-weight: bold;
          font-style: normal;
        }
      }
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
          background: rgb(66, 182, 131);
          transition: .2s ease-in;
        }
      }
    }
  }
  
  .movies {
    display: flex;
    gap: 32px 18px;
    justify-content: space-around;
    background-color: #42b683;
    padding: 48px;
    height: auto;
    flex-wrap: wrap;
    max-width: 1600px;
    margin: 0 auto;
    border-radius: 20px;
    margin-bottom: 48px;
    &.blur {
      filter:blur(4px);
    }

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
          z-index: 1;
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
          .action {
            display: flex;
            justify-content: flex-end;
            button {
              border: none;
              background: none;
              cursor: pointer;
              color: #999;
              &.edit {
                &:hover {
                  color: #42b683;
                }
              }
              &.remove {
                &:hover {
                  color: crimson;
                }
              }
              svg {
                width: 20px;
              }
            }
          }
        }
      }
    }
  }
</style>
