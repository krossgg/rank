<script>
  export let guess = "";
  export let rank = "";

  const correct = () => guess === rank;
</script>

<div class="modal">
  <div class="modal-content">
      <div class="h-full flex flex-col items-center place-content-center space-y-4 border-4">
        <h1>{correct() ? "Correct!" : "Incorrect"}</h1>
        <slot />
        <h2>Guess: {guess}, Rank: {rank}</h2>
        <button on:click>Next</button>
      </div>
  </div>
</div>

<style>
  .modal {
    position: fixed;
    left: 0;
    top: 0;
    width: 100%;
    height: 100%;
    background-color: rgba(0, 0, 0, 0.5);
    transform: scale(1.1);
  }

  .modal-content {
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    background-color: white;
    padding: 1rem 1.5rem;
    width: 40%;
    height: 30%;
    border-radius: 0.5rem;
  }
</style>
