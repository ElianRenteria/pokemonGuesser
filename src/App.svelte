<script>
  import { toasts, ToastContainer, FlatToast }  from "svelte-toasts";
  import { onMount } from 'svelte';

  
  let pokemon = { name: '', image: '' };
  let guess = '';
  let feedback = '';
  let isLoading = false;
  const API_URL = import.meta.env.VITE_API_URL
  // Function to fetch a random Pokemon from your FastAPI Pokemon API
  async function fetchRandomPokemon() {
    isLoading = true;  // Show loading
    const response = await fetch(API_URL);
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
  const showToast = (t, T, m, d, p) => {
    const toast = toasts.add({
      title: T,
      description: m,
      duration: d, // 0 or negative to avoid auto-remove
      placement: 'top-right',
      type: 'info',
      theme: 'dark',
      placement: 'top-right',
			showProgress: p,
      type: t,
      theme: 'dark',
      onClick: () => {},
      onRemove: () => {},
      // component: BootstrapToast, // allows to override toast component/template per toast
    });
  }

  // Check if the user's guess matches the Pokemon's name
  function checkGuess() {
    if (guess.toLowerCase() === pokemon.name.toLowerCase()) {
      feedback = 'Correct! 🎉';
      showToast('success', 'Correct! 🎉', '', 2500, false);
      // Generate a new Pokémon after 2 seconds
      setTimeout(() => {
        fetchRandomPokemon();
      }, 2000);
    } else {
      feedback = 'Incorrect, try again.';
      showToast('error', 'Incorrect', 'Try again', 1500, false);
    }
  }

  // Function to handle keypress event for submitting with 'Enter'
  function handleKeyPress(event) {
    if (event.key === 'Enter') {
      checkGuess();
    }
  }

  function skip_pokemon() {
    showToast('info', 'Answer:', pokemon.name, 3500, true);
    fetchRandomPokemon();
  }

  // Fetch a random Pokemon when the component is mounted
  onMount(fetchRandomPokemon);
</script>

<main>
  <div class="container">
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

    <!-- Button to check the user's guess -->
    <button on:click={checkGuess}>Submit Guess</button>
    <div class="skip__container">
      <!-- Button to fetch a new random Pokémon -->
      <button on:click={skip_pokemon} class="outline secondary skip">Next Pokémon</button>
    </div>
    <ToastContainer let:data={data}>
      <FlatToast {data}  />
    </ToastContainer>
  </div>
</main>

<style>
  main {
    text-align: center;
    padding: 2rem;
    height: 100%;
  }
  .container{
    height: 100%;
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
  .container {
    display: flex;
    flex-direction: column;
    align-items: center;
  }
  .skip__container {
    height: 100%;
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: end;
  }
  .skip {
    border: none;
  }
</style>
