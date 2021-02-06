<template>
  <div class="container">
    <h1>Element Explorer</h1>
    <form autocomplete="off" @submit.prevent="submit">
      <div class="row">
        <div class="input-field col s12">
          <input v-model="query" id="query" type="text" />
          <label class="active" for="query">Enter Name/Symbol/Number</label>
        </div>
      </div>
      <button class="btn" type="submit">Search</button>
    </form>
    <h1></h1>
    <div class="table" v-if="data.name">
      <img
        class="bohr"
        :src="`https://upload.wikimedia.org/wikipedia/commons/${md5Hash}/Electron_shell_${data.number
          .toString()
          .padStart(3, '0')}_${data.name}.svg`"
        alt="Bohr model of {{data.name}}"
      />
      <br />
      {{ data.summary }}<br />
      <a :href="data.source" class="btn-small red">Wikipedia</a>
      <table>
        <tr>
          <th>Name</th>
          <td>{{ data.name }}</td>
        </tr>
        <tr>
          <th>Atomic Number</th>
          <td>{{ data.number }}</td>
        </tr>
        <tr>
          <th>Atomic Mass</th>
          <td>{{ data.atomic_mass.toFixed(0) }}</td>
        </tr>
        <tr>
          <th>Shell Structure</th>
          <td>{{ data.shells.join(", ") }}</td>
        </tr>
        <tr>
          <th>Electronic Configuration</th>
          <td
            v-html="
              data.electron_configuration.split` `.map((x) => {
                [, f, l] = x.match(/(\d+(?:s|p|d|f))(\d+)/);
                return `${f}<sup>${l}</sup>`;
              }).join` `
            "
          ></td>
        </tr>
        <tr>
          <th>State</th>
          <td>{{ data.phase }}</td>
        </tr>
        <tr>
          <th>Discovered By</th>
          <td>{{ data.discovered_by }}</td>
        </tr>
      </table>
    </div>
    <a href="https://github.com/dipamsen/chemistry-elements">
      <img
        loading="lazy"
        width="120"
        height="120"
        src="https://github.blog/wp-content/uploads/2008/12/forkme_right_white_ffffff.png"
        class="github hide-on-med-and-down show-on-large"
        alt="Fork me on GitHub"
        data-recalc-dims="1"
      />
    </a>
  </div>
</template>

<script>
import { defineComponent, ref } from "vue";
import table from "@/assets/periodicTable.json";
import md5 from "md5";

export default defineComponent({
  name: "App",
  setup() {
    const { elements } = table;

    const names = elements.map((x) => x.name.toLowerCase());
    const symbs = elements.map((x) => x.symbol.toLowerCase());

    const query = ref("");
    const error = ref("");
    const data = ref({});
    return {
      query,
      names,
      symbs,
      elements,
      error,
      data,
    };
  },
  methods: {
    getElement(nameOrSymbol) {
      if (!Number.isNaN(parseInt(nameOrSymbol, 10))) {
        const z = parseInt(nameOrSymbol, 10);
        if (z < 0 || z > this.elements.length) {
          throw new Error("Out of Range");
        }
        return this.elements[z - 1];
      }
      const s = this.symbs.indexOf(nameOrSymbol.toLowerCase());
      if (s !== -1) return this.elements[s];
      const n = this.names.indexOf(nameOrSymbol.toLowerCase());
      if (n !== -1) return this.elements[n];
      throw new Error("Not Found");
    },
    submit() {
      try {
        const ret = this.getElement(this.query);
        this.error = "";
        this.data = ret;
      } catch (err) {
        this.data = {};
        this.error = err.message;
      }
    },
  },
  computed: {
    md5Hash() {
      const fileName = `Electron_shell_${this.data.number
        .toString()
        .padStart(3, "0")}_${this.data.name}.svg`;
      const hash = md5(fileName);
      return `${hash[0]}/${hash[0]}${hash[1]}`;
    },
  },
});
</script>

<style>
body {
  width: 100vw;
  height: 100vh;
  margin: 0;
  padding: 0;
  display: flex;
  flex-direction: column;
  align-items: center;
  overflow-x: hidden;
}

.bohr {
  min-width: 200px;
  width: 60%;
  max-width: 350px;
}

.github {
  top: 0;
  right: 0;
  position: absolute;
}

#app {
  width: 80%;
  text-align: center;
}

.error {
  background-color: red;
  font-size: large;
}

table {
  width: 60%;
  min-width: 250px;
  margin: auto;
}

input,
textarea,
button,
p,
div,
section,
article,
select {
  display: block;
}
</style>
