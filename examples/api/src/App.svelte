<script>
  import { onMount } from "svelte";
  import { writable } from "svelte/store";
  import hotkeys from "hotkeys-js";
  import { open } from "@tauri-apps/api/shell";
  import { invoke } from "@tauri-apps/api/tauri";

  import Welcome from "./components/Welcome.svelte";
  import Cli from "./components/Cli.svelte";
  import Communication from "./components/Communication.svelte";
  import Dialog from "./components/Dialog.svelte";
  import FileSystem from "./components/FileSystem.svelte";
  import Http from "./components/Http.svelte";
  import Notifications from "./components/Notifications.svelte";
  import Window from "./components/Window.svelte";
  import Shortcuts from "./components/Shortcuts.svelte";
  import Shell from "./components/Shell.svelte";
  import Updater from "./components/Updater.svelte";
  import Clipboard from "./components/Clipboard.svelte";
  import WebRTC from './components/WebRTC.svelte'
  import HttpForm from "./components/HttpForm.svelte";

  const MENU_TOGGLE_HOTKEY = 'ctrl+b';

  onMount(() => {
    hotkeys(MENU_TOGGLE_HOTKEY, () => {
      invoke('menu_toggle');
    });
  });

  const views = [
    {
      label: "Welcome",
      component: Welcome,
    },
    {
      label: "Messages",
      component: Communication,
    },
    {
      label: "CLI",
      component: Cli,
    },
    {
      label: "Dialog",
      component: Dialog,
    },
    {
      label: "File system",
      component: FileSystem,
    },
    {
      label: "HTTP",
      component: Http,
    },
    {
      label: "HTTP Form",
      component: HttpForm,
    },
    {
      label: "Notifications",
      component: Notifications,
    },
    {
      label: "Window",
      component: Window,
    },
    {
      label: "Shortcuts",
      component: Shortcuts,
    },
    {
      label: "Shell",
      component: Shell,
    },
    {
      label: "Updater",
      component: Updater,
    },
    {
      label: "Clipboard",
      component: Clipboard,
    },
    {
      label: "WebRTC",
      component: WebRTC,
    },
  ];

  let selected = views[0];

  let responses = writable([]);
  let response = ''

  function select(view) {
    selected = view;
  }

  function onMessage(value) {
    responses.update(r => [`[${new Date().toLocaleTimeString()}]` + ': ' + (typeof value === "string" ? value : JSON.stringify(value)), ...r])
  }

  function onLogoClick() {
    open("https://tauri.studio/");
  }

  onMount(() => {
    responses.subscribe(r => {
      response = r.join('\n')
    })
  })
</script>

<main>
  <div class="flex row noselect just-around" style="margin=1em;" data-tauri-drag-region>
    <img class="logo" src="tauri logo.png" height="60" on:click={onLogoClick} alt="logo" />
    <div>
      <a class="dark-link" target="_blank" href="https://tauri.studio/en/docs/getting-started/intro">
        Documentation
      </a>
      <a class="dark-link" target="_blank" href="https://github.com/tauri-apps/tauri">
        Github
      </a>
      <a class="dark-link" target="_blank" href="https://github.com/tauri-apps/tauri/tree/dev/tauri/examples/api">
        Source
      </a>
    </div>
  </div>
  <div class="flex row">
    <div style="width:15em; margin-left:0.5em">
      {#each views as view}
      <p class="nv noselect {selected === view ? 'nv_selected' : ''}" on:click={()=> select(view)}
        >
        {view.label}
      </p>
      {/each}
    </div>
    <div class="content">
      <svelte:component this={selected.component} {onMessage} />
    </div>
  </div>
  <div id="response" style="white-space: pre-line">
    <p class="flex row just-around">
      <strong>Tauri Console</strong>
      <a class="nv" on:click={()=> {
        responses.update(() => []);
        }}>clear</a>
    </p>
    {@html response}
  </div>
</main>
