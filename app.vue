vue
<template>
  <div class="body" ref="container">
    <div class="navbar" id="navbar">
      <button @click="fullScreen" id="fullscreen">
        <svg viewBox="0 0 15 15" fill="none" xmlns="http://www.w3.org/2000/svg">
          <path
            fill-rule="evenodd"
            clip-rule="evenodd"
            d="M2 2.5C2 2.22386 2.22386 2 2.5 2H5.5C5.77614 2 6 2.22386 6 2.5C6 2.77614 5.77614 3 5.5 3H3V5.5C3 5.77614 2.77614 6 2.5 6C2.22386 6 2 5.77614 2 5.5V2.5ZM9 2.5C9 2.22386 9.22386 2 9.5 2H12.5C12.7761 2 13 2.22386 13 2.5V5.5C13 5.77614 12.7761 6 12.5 6C12.2239 6 12 5.77614 12 5.5V3H9.5C9.22386 3 9 2.77614 9 2.5ZM2.5 9C2.77614 9 3 9.22386 3 9.5V12H5.5C5.77614 12 6 12.2239 6 12.5C6 12.7761 5.77614 13 5.5 13H2.5C2.22386 13 2 12.7761 2 12.5V9.5C2 9.22386 2.22386 9 2.5 9ZM12.5 9C12.7761 9 13 9.22386 13 9.5V12.5C13 12.7761 12.7761 13 12.5 13H9.5C9.22386 13 9 12.7761 9 12.5C9 12.2239 9.22386 12 9.5 12H12V9.5C12 9.22386 12.2239 9 12.5 9Z"
            fill="#000000"
          />
        </svg>
      </button>
      <h1>{{ title }}</h1>
      <button @click="close">X</button>
    </div>
    <div id="homeContainer">
      <h1>Nova V1</h1>
      <form @submit.prevent="submitForm">
        <input v-model="query" placeholder="Explore the web free!" />
      </form>
    </div>
    <div class="center" id="loading">
      <div class="wave"></div>
      <div class="wave"></div>
      <div class="wave"></div>
      <div class="wave"></div>
      <div class="wave"></div>
      <div class="wave"></div>
      <div class="wave"></div>
      <div class="wave"></div>
      <div class="wave"></div>
      <div class="wave"></div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      query: "",
      prefix: "/~/",
      title: "When loading you can always follow my tiktok ohwi...",
    };
  },
  created() {
    if (process.browser && "serviceWorker" in navigator) {
      (async () => {
        const registrations = await navigator.serviceWorker.getRegistrations();
        for (const registration of registrations) {
          await registration
            .unregister()
            .then((success) => {
              console.log("[INFO] Service Worker Successfully Unregistered");
            })
            .catch((error) => {
              console.error(
                `[INFO] Service Worker Unregistration Has Failed: ${error}`
              );
            });
        }

        await navigator.serviceWorker
          .register("/sw.js", {
            scope: this.prefix,
          })
          .then((registration) => {
            console.log(
              `[INFO] Service Worker Successfully Registered With Scope "${registration.scope}"`
            );
          })
          .catch((error) => {
            console.log(`[INFO] Service Worker Failed to Register: ${error}`);
          });
      })();
    }
  },
  methods: {
    submitForm() {
      if (this.query) {
        if (!this.isUrl(this.query))
          this.query = `https://www.google.com/search?q=${this.query}`;
        else if (
          !(
            this.query.startsWith("https://") ||
            this.query.startsWith("http://")
          )
        )
          this.query = `http://${this.query}`;

        this.loadLink(this.query);
        document.getElementById("navbar").style.display = "flex";
        document.getElementById("homeContainer").style.display = "none";
        document.getElementById("loading").style.display = "flex";
      } else {
        alert("Please enter a value.");
      }
    },
    encode(str) {
      if (!str) return str;
      return encodeURIComponent(
        str
          .toString()
          .split("")
          .map((char, ind) =>
            ind % 2 ? String.fromCharCode(char.charCodeAt() ^ 2) : char
          )
          .join("")
      );
    },
    loadLink(link) {
      if (process.browser) {
        const iframe = document.createElement("iframe");
        iframe.src = location.origin + this.prefix + this.encode(link);

        const container = this.$refs.container;
        if (container) {
          container.appendChild(iframe);

          iframe.onload = () => {
            this.title = iframe.contentDocument.title;
          };
        }
      }
    },
    isUrl(val) {
      if (
        /^http(s?):\/\//.test(val) ||
        (val.includes(".") && val.substr(0, 1) !== " ")
      )
        return true;
      return false;
    },
    fullScreen() {
      const element = document.documentElement;

      if (!document.fullscreenElement) {
        if (element.requestFullscreen) {
          element.requestFullscreen();
        } else if (element.mozRequestFullScreen) {
          element.mozRequestFullScreen();
        } else if (element.webkitRequestFullscreen) {
          element.webkitRequestFullscreen();
        } else if (element.msRequestFullscreen) {
          element.msRequestFullscreen();
        }
      } else {
        if (document.exitFullscreen) {
          document.exitFullscreen();
        } else if (document.mozCancelFullScreen) {
          document.mozCancelFullScreen();
        } else if (document.webkitExitFullscreen) {
          document.webkitExitFullscreen();
        } else if (document.msExitFullscreen) {
          document.msExitFullscreen();
        }
      }
    },
    close() {
      document.getElementById("navbar").style.display = "none";
      document.getElementsByTagName("iframe")[0].remove();
      document.getElementById("homeContainer").style.display = "flex";
      document.getElementById("loading").style.display = "none";
    },
  },
};
</script>

<style scoped>
body {
  margin: 0;
  overflow: hidden;
}

.body {
  width: 100%;
  height: 100vh;
  margin: 0;
  font-family: Arial, Helvetica, sans-serif;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  background: linear-gradient(to right, #4fc08d, #13a4ff);
}

#homeContainer {
  text-align: center;
  flex-direction: column;
}

input {
  background-color: #656464;
  text-align: center;
  border: none;
  border-radius: 5px;
  color: #01fd83;
}

input::placeholder,
