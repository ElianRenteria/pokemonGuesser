<script>
  import { onMount } from 'svelte';
  let pokemon = { name: '', image: '' };
  let guess = '';
  let feedback = '';

  // Function to fetch a random Pokemon from your FastAPI Pokemon API
  async function fetchRandomPokemon() {
    const response = await fetch('https://elianrenteria.me/api/pokemon');
    const data = await response.json();
    pokemon = {
      name: data.name,
      image: data.image,
    };
    // Clear the previous feedback and guess
    feedback = '';
    guess = '';
  }

  // Check if the user's guess matches the Pokemon's name
  function checkGuess() {
    if (guess.toLowerCase() === pokemon.name.toLowerCase()) {
      feedback = 'Correct! 🎉';
    } else {
      feedback = 'Incorrect, try again.';
    }
  }

  // Fetch a random Pokemon when the component is mounted
  onMount(fetchRandomPokemon);
</script>

<main>
  <h1>Guess the Pokémon!</h1>

  <!-- Display the random Pokémon image -->
  <img src={pokemon.image} alt="Random Pokémon" width="200" />

  <!-- Input for the user to guess the Pokémon's name -->
  <input
    type="text"
    bind:value={guess}
    placeholder="Enter Pokémon name"
  />

  <!-- Button to check the user's guess -->
  <button on:click={checkGuess}>Submit Guess</button>

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
</style>
