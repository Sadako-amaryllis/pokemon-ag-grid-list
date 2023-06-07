<template>
  <div>
    <h1>Liste des Pokémon</h1>
    <ag-grid-vue
      style="width: 100%; height: 400px;"
      class="ag-theme-alpine"
      :gridOptions="gridOptions"
      @firstDataRendered="onFirstDataRendered"
    ></ag-grid-vue>
  </div>
</template>

<script>
import axios from 'axios';
import "ag-grid-community/styles/ag-grid.css";
import "ag-grid-community/styles/ag-theme-alpine.css";
import { AgGridVue } from "ag-grid-vue3";


export default {
  components: {
    AgGridVue
  },
  data() {
    return {
      gridOptions: {
        columnDefs: [
          { headerName: 'Nom', field: 'name' },
          { headerName: 'Type', field: 'types', cellRenderer: 'arrayRenderer' },
          { headerName: 'Taille', field: 'height', valueFormatter: 'formatHeight' }
        ],
        rowData: []
      },
      frameworkComponents: {
        arrayRenderer: this.arrayRenderer,
      }
    };
  },
  methods: {
    onFirstDataRendered(params) {
      params.api.sizeColumnsToFit();
    },
    arrayRenderer(params) {
      return params.value.join(', ');
    },
    formatHeight(params) {
      return `${params.value} m`;
    },
    async loadPokemonData() {
      const response = await axios.get('https://pokeapi.co/api/v2/pokemon?limit=20');
      const pokemonList = response.data.results;
      const promises = pokemonList.map(pokemon => axios.get(pokemon.url));
      const pokemonDataList = await Promise.all(promises);
      const rowData = pokemonDataList.map(pokemonData => ({
        name: pokemonData.data.name,
        types: pokemonData.data.types.map(type => type.type.name),
        height: pokemonData.data.height
      }));
      this.gridOptions.rowData = rowData;
    }
  },
  mounted() {
    this.loadPokemonData();
  }
};
</script>
