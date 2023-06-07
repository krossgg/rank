<script>
  import { onMount, onDestroy } from "svelte";
  import Modal from "../components/modal.svelte";
  import { Client } from "tmi.js";
  export let data;

  let index = 0;
  let score = 0;

  let guessing = true;
  const toggle = () => (guessing = !guessing);

  let done = false;
  let guess = "";

  let msgs = [];

  const clips = data.clips;
  const ranks = data.ranks;

  $: url = clips[index].url;
  $: rank = clips[index].rank;

  let chat = {};
  ranks.forEach((r, i) => (chat[r] = i));

  const client = Client({
    channels: ["ggkross"],
  });

  onMount(() => {
    client.connect();
    client.on("message", (channel, tags, message, self) => {
      var msg = `${tags["display-name"]}: ${message}`;
      if (msgs.length > 10) msgs.shift();
      msgs = [...msgs, msg];
      if (guessing && ranks.includes(message)) {
        chat[message] += 1;
      }
    });
  });

  function onGuess(g) {
    guess = g;
    toggle();
  }

  function onNext() {
    if (guess === rank) score += 1;
    if (index === 2) done = true;
    else index += 1;
    toggle();
    ranks.forEach((r, i) => (chat[r] = i));
    msgs = [];
  }
</script>

<div class="h-screen flex flex-row items-center justify-center border-4">
{#if !done}
  {#if guessing}
  <div class="w-1/6 m-6"/>
  <div class="h-2/3 w-1/2 flex flex-col items-center justify-center border-4 space-y-2">
      <div>
        <iframe
          width="1020"
          height="630"
          src={url}
          title="YouTube video player"
          frameborder="0"
          allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share"
          allowfullscreen
        />
      </div>
      <div class='w-fit flex flex-row items-center justify-center'>
        {#each ranks as rank}
            <button type="button" class='mx-1 border-cyan-300 border-2 p-1' on:click={() => onGuess(rank)}
              >{rank}</button
            >
        {/each}
      </div>
      <em>Score: {score}, index: {index}</em>
  </div>
  <div class="h-2/3 w-1/6 flex flex-col space-y-1 border-4 m-6">
    <div class="font-bold p-2">Chat</div>
    <ul style="list-style-type: none" class="flex flex-col space-y-1 border-4 p-3">
      {#each msgs as msg}
        <li class="font-sans">{msg}</li>
      {/each}
    </ul>
  </div>
  {:else}
    <Modal {guess} {rank} on:click={onNext}>
      <div class="w-full flex flex-row items-center justify-items-center place-content-center space-x-4">
        {#each ranks as rank}
        <div class="h-fit flex flex-col items-center border-2 w-20">
          <div class="font-bold">{chat[rank]}</div>
            <div>
              <svg>
                <rect x="20" y="{100 - chat[rank]*4}" width="10" height="{chat[rank] * 4}" style="fill: #ff0"></rect>
              </svg>
            </div>
            <div>{rank}</div>
        </div>
        {/each}
      </div>
    </Modal>
  {/if}
{:else}
  <h1>Done, Score: {score}</h1>
  <button type="button" class='mx-1 border-cyan-300 border-2 p-1' on:click={() => {done = !done; index = 0; score=0}}>Redo</button>
{/if}
</div>

<style>
  svg {
		position: relative;
		width: 50px;
		height: 100px;
	}
</style>
