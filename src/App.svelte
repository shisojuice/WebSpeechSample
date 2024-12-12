<script>
  import { onMount } from "svelte";

  const dark = "dark";
  const light = "light";
  let theme = $state(light);
  let voices = $state([]);
  let voiceIx = $state(0);
  let volume = $state(1);
  let pitch = $state(1);
  let rate = $state(1);
  let text = $state("");

  onMount(() => {
    voices = window.speechSynthesis.getVoices();
    if (
      voices.length > 0 &&
      (voiceIx === undefined || voiceIx >= voices.length)
    ) {
      voiceIx = 0;
    }
    window.speechSynthesis.onvoiceschanged = () => {
      voices = window.speechSynthesis.getVoices();
    };
    const savedTheme = localStorage.getItem("theme");
    if (savedTheme) {
      theme = savedTheme;
    } else {
      const prefersDark = window.matchMedia(
        "(prefers-color-scheme: dark)",
      ).matches;
      theme = prefersDark ? dark : light;
    }
    updateTheme();
  });

  $effect(() => {
    if (typeof document !== "undefined") {
      localStorage.setItem("theme", theme);
      updateTheme();
    }
  });

  const updateTheme = () => {
    document.body.classList.remove(light, dark);
    document.body.classList.add(theme);
  };

  const toggleTheme = () => {
    theme = theme === dark ? light : dark;
  };

  const Speech = () => {
    const utterance = new SpeechSynthesisUtterance();
    utterance.voice = voices[voiceIx];
    utterance.lang = voices[voiceIx].lang;
    utterance.volume = volume;
    utterance.pitch = pitch;
    utterance.rate = rate;
    utterance.text = text;
    speechSynthesis.speak(utterance);
  };
</script>

<header>
  <button class="headbtn" onclick={toggleTheme}>🌞/🌛</button>
</header>
<main>
  <h1>Web Speech Sample</h1>
  <div class="set">
    <label for="voicetext">VoiceText</label>
    <input type="text" id="voicetext" maxlength="50" bind:value={text} />
  </div>
  <div class="set">
    <label for="voice">Voice</label>
    <select name="voice" id="voice" bind:value={voiceIx}>
      {#each voices as voice, i}
        <option value={i}>{voice.name}</option>
      {/each}
    </select>
  </div>
  <div class="set">
    <label for="volume">Volume_{volume}</label>
    <input
      type="range"
      min="0"
      max="1"
      step="0.1"
      id="volume"
      bind:value={volume}
    />
  </div>
  <div class="set">
    <label for="pitch">Pitch_{pitch}</label>
    <input
      type="range"
      min="0"
      max="2"
      step="0.1"
      id="pitch"
      bind:value={pitch}
    />
  </div>
  <div class="set">
    <label for="rate">Rate_{rate}</label>
    <input
      type="range"
      min="0.1"
      max="10"
      step="0.1"
      id="rate"
      bind:value={rate}
    />
  </div>
  <button onclick={Speech}>Speech</button>
</main>
<footer>
  <span>Only works properly in browsers with Web Speech API enabled.</span>
  <span>
    © 2024 <a
      href="https://github.com/shisojuice"
      target="_blank"
      rel="noopener noreferrer">shisojuice</a
    > Pomodoro Timer. All rights reserved.</span
  >
</footer>
