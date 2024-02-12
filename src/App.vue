<script>
export default {
  data() {
    return {
      localPort: "",
      portStatus: "",
      metaTags: [],
      loading: false,
    };
  },
  computed: {
    statusStyle() {
      if (this.portStatus.includes("open")) {
        return { color: "green" };
      } else if (this.portStatus.includes("closed")) {
        return { color: "red" };
      } else {
        return { color: "yellow" };
      }
    },
  },
  methods: {
    async fetchMetaTags() {
      const url = `http://localhost:${this.localPort}`;
      
      try {
        const response = await fetch(url);
        if (response.status === 200) {
          const html = await response.text();
          const parser = new DOMParser();
          const doc = parser.parseFromString(html, "text/html");
          const metaTags = Array.from(doc.head.querySelectorAll("meta"));

          if (metaTags.length === 0) {
            console.warn("No meta tags found in the header of the specified URL");
            return;
          }

          this.metaTags = metaTags.map((tag) => ({
            name: tag.getAttribute("name") || "",
            property: tag.getAttribute("property") || "",
            content: tag.getAttribute("content") || "",
          }));
        } else {
          console.warn(`Unable to fetch meta tags. Status code: ${response.status}`);
        }
      } catch (error) {
        console.error("Error retrieving HTML content:", error);
      }
    },

    async checkPort() {
      this.loading = true;
      // const portToCheck = parseInt(this.localPort, 10);
      const portToCheck = parseInt(this.localPort);
      if (isNaN(portToCheck)) {
        this.portStatus = "Please enter a valid port number.";
        this.loading = false;
        return;
      }

      try {
        const response = await fetch(`http://localhost:${portToCheck}`);
        if (response.status === 200) {
          this.portStatus = `Port ${portToCheck} is open.`;
          await this.fetchMetaTags();
        } else {
          this.portStatus = `Port ${portToCheck} is closed or blocked.`;
        }
      } catch (error) {
        this.portStatus = `Error checking port ${portToCheck}`;
        // this.portStatus = `Error checking port ${portToCheck}: ${error.message}`;
      }

      this.loading = false;
    },
  },
};
</script>

<template>
  <div class="flex flex-col h-screen items-center justify-center gap-20 p-6">
    <header class="flex flex-col items-center gap-7">
      <form
        @submit.prevent="checkPort"
        class="flex flex-row items-center gap-2"
      >
        <input
          type="text"
          value=""
          v-model="localPort"
          placeholder="Place your local port"
          class="search-input border rounded p-2 outline-none text-sm"
        />
        <button
          :class="{ loading: loading }"
          :disabled="loading"
          class="bg-slate-950 text-gray-50 border-none px-2 p-1 border rounded text-base"
        >
          <span v-if="!loading">Check Port</span>
          <span v-else>Loading...</span>
        </button>
      </form>

      <div class="flex items-center justify-center">
        <p :style="statusStyle" class="uppercase font-semibold">
          {{ portStatus }}
        </p>
      </div>
    </header>
    <table>
        <thead>
          <tr>
            <th>Name</th>
            <th>Propertyo</th>
            <th>Content</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="(metaTag, index) in metaTags" :key="index">
            <td>{{ metaTag.name }}</td>
            <td>{{ metaTag.property }}</td>
            <td>{{ metaTag.content }}</td>
          </tr>
        </tbody>
      </table>
    <main v-if="portStatus.includes('open')" class="flex flex-row gap-7">
      <div class="border rounded">
        <div class="">
          <img src="/og-image.png" alt="OG" class="og-img" />
        </div>
        <div
          class="flex flex-col items-start justify-start p-2 gap-1 bg-zinc-200/45 border-t-2"
        >
          <a href="" class="uppercase text-[12px]">Link</a>
          <h4 class="font-semibold text-base">Título</h4>
          <p class="text-sm">I want to put a ding in the universe ▲</p>
        </div>
      </div>
      <div class="border rounded">
        <div class="">
          <img src="/og-image.png" alt="OG" class="og-img" />
        </div>
        <div
          class="flex flex-col items-start justify-start p-2 gap-1 bg-zinc-200/45 border-t-2"
        >
          <a href="" class="uppercase text-[12px]">Link</a>
          <h4 class="font-semibold text-base">Título</h4>
          <p class="text-sm">I want to put a ding in the universe ▲</p>
        </div>
      </div>
      <div class="border rounded">
        <div class="">
          <img src="/og-image.png" alt="OG" class="og-img" />
        </div>
        <div
          class="flex flex-col items-start justify-start p-2 gap-1 bg-zinc-200/45 border-t-2"
        >
          <a href="" class="uppercase text-[12px]">Link</a>
          <h4 class="font-semibold text-base">Título</h4>
          <p class="text-sm">I want to put a ding in the universe ▲</p>
        </div>
      </div>
    </main>
    <main v-else class="flex flex-col items-center justify-center gap-4">
      <img
        src="https://illustrations.popsy.co/white/success.svg"
        alt="Ilustration"
        class="ilustration"
      />
      <h1
        class="scroll-m-20 text-4xl font-extrabold tracking-tight lg:text-5xl"
      >
        Analyze metrics locally
      </h1>
      <p class="text-xl text-muted-foreground">
        Just enter the port number you want and voila
      </p>
    </main>
  </div>
  <footer class="flex items-center justify-center border-t my-4 md:my-7">
    <div class="mt-9 sm:mb-3">
      <small class="text-sm font-normal leading-none">
        2024 Built by
        <a href="https://twitter.com/euotiniel">@euotiniel</a> . Hosted on
        <a href="https://vercel.com/"> ▲ </a>
      </small>
    </div>
  </footer>
</template>

<style scoped>
.search-input {
  width: 350px;
}
.og-img {
  height: 180px;
  object-fit: cover;
}
.ilustration {
  height: 180px;
  object-fit: cover;
}
</style>
