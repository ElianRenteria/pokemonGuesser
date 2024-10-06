<script>
  import { toasts, ToastContainer, FlatToast }  from "svelte-toasts";
  import { onMount } from 'svelte';

  let pokemon = { name: '', image: '' };
  let guess = '';
  let feedback = '';
  let isLoading = false;
  const API_URL = import.meta.env.VITE_API_URL;

  async function fetchRandomPokemon() {
    if (isLoading) return; 
    isLoading = true; 
    try {
      const response = await fetch(API_URL);
      const data = await response.json();
      pokemon = {
        name: data.name,
        image: data.image,
      };
      feedback = '';
      guess = '';
    } catch (error) {
      console.error('Error fetching Pokemon:', error);
    } finally {
      isLoading = false; 
    }
  }

  const showToast = (t, T, m, d, p) => {
    toasts.add({
      title: T,
      description: m,
      duration: d,
      placement: 'top-right',
      type: t,
      theme: 'dark',
      showProgress: p,
    });
  };

  function checkGuess() {
    if (guess.toLowerCase() === pokemon.name.toLowerCase()) {
      feedback = 'Correct! 🎉';
      showToast('success', 'Correct! 🎉', '', 2500, false);
      isLoading = true; 
      setTimeout(() => {
        fetchRandomPokemon();
      }, 2000);
    } else {
      feedback = 'Incorrect, try again.';
      showToast('error', 'Incorrect', 'Try again', 1500, false);
    }
  }

  function handleKeyPress(event) {
    if (event.key === 'Enter') {
      checkGuess();
    }
  }

  function skip_pokemon() {
    if (!isLoading) {
      showToast('info', 'Answer:', pokemon.name, 3500, true);
      fetchRandomPokemon();
    }
  }

  onMount(fetchRandomPokemon);
</script>

<main>
  <div class="container">
    <h1>Guess the Pokémon!</h1>

    {#if isLoading}
      <article aria-busy="true"></article>
    {/if}

    {#if !isLoading}
      <img src={pokemon.image} alt="Random Pokémon" width="200" />
    {/if}

    <input
      type="text"
      bind:value={guess}
      placeholder="Enter Pokémon name"
      on:keypress={handleKeyPress}
      disabled={isLoading}
    />

    {#if feedback}
      <p>{feedback}</p>
    {/if}

    <button on:click={checkGuess} disabled={isLoading}>Submit Guess</button>

    <div class="skip__container">
      <button on:click={skip_pokemon} class="outline secondary skip" disabled={isLoading}>Next Pokémon</button>
    </div>

    <ToastContainer let:data={data}>
      <FlatToast {data} />
    </ToastContainer>
  </div>
</main>

<style>
  main {
    text-align: center;
    padding: 2rem;
    height: 100%;
  }
  .container {
    height: 100%;
    display: flex;
    flex-direction: column;
    align-items: center;
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
