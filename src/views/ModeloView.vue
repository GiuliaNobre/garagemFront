<script setup>
import { ref, reactive, onMounted } from "vue";
import ModelosApi from "@/api/modelos";
const modelosApi = new ModelosApi();

const defaultModelo = { id: null, nome: "", marca: "", categoria: "" };
const modelos = ref([]);
const modelo = reactive({ ...defaultModelo });

onMounted(async () => {
  modelos.value = await modelosApi.buscarTodosOsModelos();
});

function limpar() {
  Object.assign(modelo, { ...defaultModelo });
}

async function salvar() {
  if (modelo.id) {
    await modelosApi.atualizarModelo(modelo);
  } else {
    await modelosApi.adicionarModelo(modelo);
  }
  modelos.value = await modelosApi.buscarTodosOsModelos();
  limpar();
}

function editar(modelo_para_editar) {
  Object.assign(modelo, modelo_para_editar);
}

async function excluir(id) {
  await modelosApi.excluirModelo(id);
  modelos.value = await modelosApi.buscarTodosOsModelos();
  limpar();
}
</script>

<template>
  <h1>Modelo</h1>
  
  <div class="form">
    <input type="text" v-model="modelo.nome" placeholder="Nome" />
    
    <select v-model="modelo.marca"  >
      <option value=""></option> placeholder="Marca"
    </select>

    <button @click="salvar">Salvar</button>
    <button @click="limpar">Limpar</button>
  </div>
  
  <ul>
    <li v-for="modelo in modelos" :key="modelo.id">
      <span @click="editar(modelo)">
        ({{ modelo.id }}) - {{ modelo.nome }} - {{ modelo.marca }} - {{ modelo.categoria }}
      </span>
      <button @click="excluir(modelo.id)">X</button>
    </li>
  </ul>
</template>

<style scoped>

</style>