<script>
  import { onMount } from "svelte";
  import axios from "axios";

  let prompt = "";
  let responses = [];
  let question = "";

  async function sendPrompt() {
    try {
      const response = await axios.post("/prompt", { text: prompt });
      if (response.status === 200) {
        console.log("Prompt sent successfully");
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
</script>



    
<style>
  .container {
    display: flex;
    flex-direction: column;
    align-items: center;
  }

  .input-container {
    display: flex;
    margin-bottom: 1rem;
  }

  .response {
    background-color: #f0f0f0;
    padding: 1rem;
    margin: 0.5rem;
    border-radius: 0.5rem;
  }
</style>

<div class="container">
  <h1>Prompts AgainstaljkdfnHumanity</h1>
  <h1>{question}</h1>
  <div class="input-container">
    <input
      type="text"
      bind:value="{prompt}"
      placeholder="Type your prompt here..."
    />
    <button on:click="{sendPrompt}">Send Prompt</button>
  </div>
  <button on:click="{getResponses}">Get Responses</button>
  <button on:click="{next}">Next</button>

  {#each responses as response}
    <div class="response">{response}</div>
  {/each}
</div>

