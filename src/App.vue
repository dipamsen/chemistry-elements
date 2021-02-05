<template>
  <div class="container">
    <h1>Periodic Table</h1>
    <div class="row">
      <div class="input-field col s12">
        <input v-model="query" id="query" type="text" />
        <label class="active" for="query">Enter Name/Symbol/Number</label>
      </div>
    </div>
    <button class="btn" @click="submit">Search</button>
    <h1></h1>
    <div class="table" v-if="data.name">
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
          <th>Electronic Configuration</th>
          <td>{{ data.shells.join(", ") }}</td>
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
    <div class="error white-text" v-if="error">{{ error }}</div>
  </div>
</template>

<script lang="ts">
import { defineComponent, ref } from "vue";
import table from "@/assets/periodicTable.json";

type Elt = typeof table.elements[number];
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
    getElement(nameOrSymbol: string): Elt {
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
