<script lang="ts">
    import { onMount } from "svelte";

    let speech = '';
    let history: string[] = [];

    let recognition: SpeechRecognition;


  onMount(() => {
    // FYI. https://developer.mozilla.org/ja/docs/Web/API/SpeechRecognition
    const SpeechRecognition = window.SpeechRecognition || webkitSpeechRecognition;
    // const SpeechGrammarList = window.SpeechGrammarList || webkitSpeechGrammarList;
    // const SpeechRecognitionEvent =
    // window.SpeechRecognitionEvent || webkitSpeechRecognitionEvent;

    recognition = new SpeechRecognition();
    recognition.lang = 'ja-JP'
    recognition.interimResults = true;
    recognition.continuous = true;

    
    recognition.onresult = (event) => {
      let joined = '';
      for (let index = 0; index < event.results.length; index++) {
        joined = joined + event.results[index][0].transcript;
      }

      speech = joined;
      scrollBottom();
    }
  });

  function start(): void {
    recognition.start();
    scrollBottom();
    return;
  }

  function stop(): void {
    history =   [...history, speech];
    recognition.stop()

    // FixME
    setTimeout(() => {
      speech = '';
    }, 100);
    return;
  }

  function scrollBottom(): void {
    // scroll setting
    const current =  document.getElementById('current-speech');

    if(current) {
      current.scrollIntoView({ inline: 'end' });
    }
  }
</script>

<div class="main">
  <div class="contents">
    <div id="original-speech-box" class="scrollable original-speech">

      {#each history as log }
      <p>{log}</p>
      {/each}
      <p id="current-speech">{speech}</p>
    </div>

    <div class="scrollable transleated">
      transleated
    </div>
  </div>
    
  <div class="footer">
    <button on:click={start}>start</button>
    <button on:click={stop}>stop</button>
  </div>
</div>

<style>
  .main {
    height: 100dvh;
    width: 100%;
  }
  .contents {
    height: calc(100dvh - 60px);
  }
  .footer {
    height: 60px;
    width: 100%;
    background-color: skyblue;
    display: flex;
    align-items: center;
    justify-content: center;
    gap: 1em;
  }

  .original-speech {
    background-color: gainsboro;
    height: 50%;
  }

  .transleated{
    background-color: azure;
    height: 50%;
  }

  .scrollable{
    overflow-y: scroll;
    overflow-wrap: break-word;
  }

  #current-speech {
    background-color: #f6f695;
  }
</style>
