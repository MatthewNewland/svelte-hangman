<script lang="ts">
  import { onMount } from "svelte"

  const handleKey = (key: string) => {
    if (!/^[A-Za-z]$/.test(key)) {
      return
    }

    keyCount++
    key = key.toUpperCase()

    if (!word.includes(key)) {
      return
    }

    for (let [i, c] of word.split("").entries()) {
      if (c === key) {
        letters[i] = key
      }
    }
  }

  let letters: string[] = []
  let word: string = ""

  const qwerty = [
    "qwertyuiop".toUpperCase().split(""),
    "asdfghjkl".toUpperCase().split(""),
    "zxcvbnm".toUpperCase().split(""),
  ]

  $: won = letters && letters.filter((x) => x === " ").length === 0

  const reset = () => {
    const WORDS = ["heath", "paint", "champ", "gourd", "california"]
    word = WORDS[Math.floor(Math.random() * WORDS.length)].toUpperCase()
    letters = []
    for (let i = 0; i < word.length; i++) {
      letters.push(" ")
    }
    maxCount = word.length + 5
    keyCount = 0
  }

  onMount(reset)

  let keyCount = 0
  let maxCount = 10

  const handleKeydown = (e: KeyboardEvent) => {
    if (e.ctrlKey && e.key === "q") {
      reset()
      return
    }

    handleKey(e.key)
  }

  $: lost = keyCount >= maxCount && !won
</script>

<svelte:window on:keydown={handleKeydown} />

<main class="grid place-items-center mx-auto w-1/3 mt-10 gap-5">
  <h1 class="my-5 text-3xl font-bold">Hangman</h1>
  <span class="ml-5">Remaining guesses: {maxCount - keyCount}</span>
  <div class="flex flex-row gap-2">
    {#each letters as letter}
      <span class="border-b whitespace-pre text-3xl text-center p-3 w-[1.5em]">
        {letter}
      </span>
    {/each}
  </div>
  <div class="flex flex-col gap-2 place-items-center">
    {#each qwerty as row}
      <div class="flex flex-row gap-1">
        {#each row as key}
          <button
            class="px-4 py-2 aspect-square rounded shadow bg-blue-600"
            on:click={() => handleKey(key)}
          >
            {key}
          </button>
        {/each}
      </div>
    {/each}
  </div>
  {#if won}
    <p class="text-2xl text-green-600">You won!</p>
    <button class="px-4 py-2 rounded shadow bg-blue-600" on:click={reset}>
      Reset
    </button>
  {:else if lost}
    <p class="text-red-600">You lost :/</p>
  {/if}
</main>
