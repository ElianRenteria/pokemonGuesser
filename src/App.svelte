<script>
  import { onMount } from 'svelte';
  let pokemon = { name: '', image: '' };
  let guess = '';
  let feedback = '';
  let isLoading = false;

  // Function to fetch a random Pokemon from your FastAPI Pokemon API
  async function fetchRandomPokemon() {
    isLoading = true;  // Show loading
    const response = await fetch('https://elianrenteria.me/api/pokemon');
    const data = await response.json();
    pokemon = {
      name: data.name,
      image: data.image,
    };
    // Clear the previous feedback and guess
    feedback = '';
    guess = '';
    isLoading = false;  // Hide loading
  }

  // Check if the user's guess matches the Pokemon's name
  function checkGuess() {
    if (guess.toLowerCase() === pokemon.name.toLowerCase()) {
      feedback = 'Correct! 🎉';

      // Generate a new Pokémon after 2 seconds
      setTimeout(() => {
        fetchRandomPokemon();
      }, 2000);
    } else {
      feedback = 'Incorrect, try again.';
    }
  }

  // Function to handle keypress event for submitting with 'Enter'
  function handleKeyPress(event) {
    if (event.key === 'Enter') {
      checkGuess();
    }
  }

  // Fetch a random Pokemon when the component is mounted
  onMount(fetchRandomPokemon);
</script>

<main>
  <h1>Guess the Pokémon!</h1>

  <!-- Show loading text while fetching -->
  {#if isLoading}
    <article aria-busy="true"></article>
  {/if}

  <!-- Display the random Pokémon image when loaded -->
  {#if !isLoading}
    <img src={pokemon.image} alt="Random Pokémon" width="200" />
  {/if}

  <!-- Input for the user to guess the Pokémon's name, handles 'Enter' key -->
  <input
    type="text"
    bind:value={guess}
    placeholder="Enter Pokémon name"
    on:keypress={handleKeyPress}
  />

  <!-- Feedback to show whether the guess was correct or not -->
  {#if feedback}
    <p>{feedback}</p>
  {/if}

  <!-- Button to fetch a new random Pokémon -->
  <button on:click={fetchRandomPokemon}>Next Pokémon</button>
</main>

<style>
  main {
    text-align: center;
    padding: 2rem;
  }
  img {
    margin-bottom: 1rem;
  }
  button {
    margin-top: 1rem;
  }
  input {
    width: 60%;
    min-width: 100px;
  }
</style>
