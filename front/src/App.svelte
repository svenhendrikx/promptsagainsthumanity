<script>
  import { onMount } from "svelte";
  import axios from "axios";

  let prompt = "";
  let responses = [];
  let question = "";
  let error= "";
  let row = Array.from([ 5 ], (_, i) => i + 1);

  async function sendPrompt() {
    try {
      const response = await axios.post("/prompt", { text: prompt });
      if (!prompt.includes('{}')) {
          error = 'String does not include {}.'
          return
      }
      error = '';
      if (response.status === 200) {
        console.log("Prompt sent successfully");
        prompt = "";
      }
    } catch (error) {
      console.error("Error sending prompt:", error);
    }
  }

  async function reset() {
    try {
      const response = await axios.get("/reset");
      if (response.status === 200) {
        question = response.data.question;
        console.log(question);
        prompt = "";
        await clear()
      }
    } catch (error) {
      console.error("Error sending prompt:", error);
    }
  }

  async function next() {
    try {
      const response = await axios.get("/next");
      if (response.status === 200) {
        question = response.data.question;
        console.log(question);

      }
    } catch (error) {
      console.error("Error sending prompt:", error);
    }
  }
  async function getResponses() {
    try {
      const response = await axios.get("/getresponses");
      if (response.status === 200) {
        responses = response.data.responses;
        console.log(response)
        console.log(responses)
      }
    } catch (error) {
      console.error("Error getting responses:", error);
    }
  }

  async function getQuestion() {
    try {
      const response = await axios.get("/getquestion");
      if (response.status === 200) {
        question = response.data.question;
      }
    } catch (error) {
      console.error("Error getting question:", error);
    }
  }
  async function clear() {
    try {
      responses = []
    } catch (error) {
      console.error("Error getting responses:", error);
    }
  }
</script>
<style>
  :global(body) {
    margin: 0;
    font-family: 'Georgia', serif;
    background-color: #f3f3f3;
  }

  .container {
    max-width: 600px;
    margin: 0 auto;
    padding: 2rem;
    position: relative;
  }

  .input-container {
    display: flex;
    flex-direction: column;
    margin-bottom: 2rem;
  }

  .input-container input {
    padding: 0.5rem;
    margin-bottom: 1rem;
    font-size: 1rem;
  }

  button {
    background-color: #6a9cc6;
    color: white;
    padding: 0.5rem 1rem;
    border: none;
    border-radius: 0.25rem;
    cursor: pointer;
    transition: transform 0.2s;
    font-size: 1rem;
  }

  button:hover {
    background-color: #5a8cb6;
  }

  button:active {
    transform: scale(0.95);
  }

  .grid-container {
    display: grid;
    grid-template-columns: repeat(auto-fill, minmax(200px, 1fr));
    gap: 1rem;
  }

  .error {
    border: 2px solid red;
    border-radius: 0.5rem;
    background-color: #FDD;
    
    margin-top: 2px;
    margin-bottom: 2px;
    padding: 10px;
  }
  .response {
    background-color: #ffffff;
    padding: 1rem;
    border-radius: 0.5rem;
    text-align: center;
    display: flex;
    align-items: center;
    justify-content: center;
    height: 200px;
    box-shadow: 0px 2px 4px rgba(0, 0, 0, 0.1);
    transition: transform 0.3s;
  }

  .response:hover {
    transform: scale(1.05);
    box-shadow: 0px 4px 8px rgba(0, 0, 0, 0.2);
  }
</style>


<svelte:window  on:load={getQuestion}/>
<div class="container">
  <h1>Prompts Against Humanity</h1>
  <h2>{question}</h2>
  <div class="input-container">
    {#if error}

    <div class = 'error'>{error}</div>
    {/if}
    <input
      type="text"
      bind:value="{prompt}"
      placeholder="Type your prompt here..."
    />
    <button on:click="{sendPrompt}">Send Prompt</button>
  </div>
  <button on:click="{getResponses}">Get Responses</button>
  <button on:click="{next}">Next</button>
  <button on:click="{reset}">Reset</button>
  <button on:click="{clear}">Clear</button>

  <div class="grid-container">
    {#each responses as response, index (index)}
      <div class="response">{response}</div>
    {/each}
  </div>
</div>

