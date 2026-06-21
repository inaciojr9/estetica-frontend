<template>
  <div class="min-h-screen bg-pink-50 text-gray-800 font-sans">

    <!-- 1. POP-UP DE MAIORIDADE (Bloqueio de Segurança para a Área Pública) -->
    <div v-if="!maiorDeIdade && abaAtiva === 'publico'" class="fixed inset-0 bg-black bg-opacity-95 z-50 flex flex-col items-center justify-center p-6 text-center">
      <div class="bg-white p-8 rounded-2xl max-w-md shadow-2xl">
        <span class="text-4xl">⚠️</span>
        <h2 class="text-2xl font-bold text-gray-900 mt-4 mb-2">Conteúdo Sensível</h2>
        <p class="text-gray-600 mb-6">Este portfólio contém fotos reais de procedimentos estéticos íntimos corporais. Você tem mais de 18 anos?</p>
        <div class="flex gap-4 justify-center">
          <button @click="confirmarMaioridade" class="bg-pink-600 hover:bg-pink-700 text-white font-semibold px-6 py-2 rounded-lg transition">Sim, tenho 18+</button>
          <button @click="sairDoSite" class="bg-gray-200 hover:bg-gray-300 text-gray-700 font-semibold px-6 py-2 rounded-lg transition">Sair</button>
        </div>
      </div>
    </div>

    <!-- 2. BARRA DE NAVEGAÇÃO SUPERIOR -->
    <nav class="bg-white shadow-sm border-b border-pink-100 px-6 py-3 flex justify-between items-center">
      <div class="text-left">
        <h1 class="text-xl font-serif font-bold text-pink-800 tracking-wide">Dra. Roberta Silva</h1>
        <p class="text-xs uppercase tracking-widest text-gray-400">Estética Avançada</p>
      </div>
      <!-- Abas para alternar entre a visão da Cliente e o Painel da Esteticista -->
      <div class="flex gap-2">
        <button @click="abaAtiva = 'publico'" :class="['px-4 py-1.5 rounded-full text-xs font-semibold transition', abaAtiva === 'publico' ? 'bg-pink-600 text-white shadow' : 'bg-gray-100 text-gray-600 hover:bg-gray-200']">Ver Portfólio</button>
        <button @click="abaAtiva = 'admin'" :class="['px-4 py-1.5 rounded-full text-xs font-semibold transition', abaAtiva === 'admin' ? 'bg-gray-800 text-white shadow' : 'bg-gray-100 text-gray-600 hover:bg-gray-200']">Painel Restrito</button>
      </div>
    </nav>

    <!-- 3. ABA: PORTFÓLIO PÚBLICO (Visão das Clientes do Instagram) -->
    <main v-if="abaAtiva === 'publico'" class="max-w-4xl mx-auto p-4 py-8">
      <div v-if="carregando" class="text-center text-gray-500 py-12">Carregando portfólio profissional...</div>
      <div v-else-if="procedimentos.length === 0" class="text-center text-gray-400 py-12">Nenhum procedimento publicado ainda.</div>

      <div v-else class="space-y-12">
        <article v-for="item in procedimentos" :key="item.id" class="bg-white rounded-2xl shadow-md overflow-hidden border border-pink-100">

          <!-- Container da Imagem/Vídeo com Blur Inteligente para Região Íntima -->
          <div class="relative bg-gray-950 flex items-center justify-center overflow-hidden aspect-video">
            <img :src="item.urlMidia" :class="['w-full h-full object-cover transition-all duration-500', item.tipo === 'INTIMO' && !revelados.includes(item.id) ? 'blur-2xl scale-110 pointer-events-none' : '']" alt="Resultado"/>

            <!-- Botão de "Clique para ver" nas fotos marcadas como INTIMO -->
            <div v-if="item.tipo === 'INTIMO' && !revelados.includes(item.id)" class="absolute inset-0 bg-black/40 flex flex-col items-center justify-center p-4 text-center">
              <p class="text-white text-xs font-medium mb-3">Foto clínica de região íntima feminina</p>
              <button @click="revelados.push(item.id)" class="bg-white hover:bg-pink-50 text-gray-900 font-semibold px-5 py-2 rounded-full text-xs uppercase tracking-wider shadow transition">Visualizar Caso</button>
            </div>
          </div>

          <!-- Detalhes do Caso (Título + Texto Explicativo) -->
          <div class="p-6">
            <div class="flex items-center gap-2 mb-2">
              <span :class="['text-xs font-bold uppercase tracking-wider px-2.5 py-0.5 rounded-full', item.tipo === 'INTIMO' ? 'bg-purple-100 text-purple-800' : 'bg-pink-100 text-pink-800']">
                {{ item.tipo === 'INTIMO' ? 'Íntimo' : 'Rosto' }}
              </span>
            </div>
            <h3 class="text-lg font-bold text-gray-900 mb-2">{{ item.titulo }}</h3>
            <p class="text-gray-600 text-sm leading-relaxed whitespace-pre-line">{{ item.textoExplicativo }}</p>
          </div>
        </article>
      </div>
    </main>

    <!-- 4. ABA: PAINEL ADMINISTRATIVO (Exclusivo para a Esteticista) -->
    <main v-if="abaAtiva === 'admin'" class="max-w-xl mx-auto p-4 py-12">
      <div class="bg-white rounded-2xl shadow-lg border border-gray-100 p-8">
        <h2 class="text-xl font-bold text-gray-800 mb-1">Publicar Novo Caso</h2>
        <p class="text-xs text-gray-400 mb-6">Preencha o relatório explicativo e suba a foto do resultado.</p>

        <form @submit.prevent="enviarProcedimento" class="space-y-4">
          <div>
            <label class="block text-xs font-bold text-gray-600 mb-1">Título do Tratamento</label>
            <input v-model="form.titulo" type="text" placeholder="Ex: Clareamento Íntimo - Sessão 2" required class="w-full border rounded-lg px-3 py-2 text-sm focus:ring-2 focus:ring-pink-500 focus:outline-none"/>
          </div>
          <div>
            <label class="block text-xs font-bold text-gray-600 mb-1">Tipo de Procedimento</label>
            <select v-model="form.tipo" class="w-full border rounded-lg px-3 py-2 text-sm bg-white focus:outline-none focus:ring-2 focus:ring-pink-500">
              <option value="ROSTO">Limpeza de Pele (Rosto)</option>
              <option value="INTIMO">Estética Íntima (Feminina)</option>
            </select>
          </div>
          <div>
            <label class="block text-xs font-bold text-gray-600 mb-1">Texto Clínico Explicativo</label>
            <textarea v-model="form.textoExplicativo" rows="4" placeholder="Descreva os ácidos usados, evolução da pele, cuidados tomados..." required class="w-full border rounded-lg px-3 py-2 text-sm focus:ring-2 focus:ring-pink-500 focus:outline-none resize-none"></textarea>
          </div>
          <div>
            <label class="block text-xs font-bold text-gray-600 mb-1">Foto/Vídeo do Resultado</label>
            <input type="file" accept="image/*,video/*" @change="aoSelecionarArquivo" required class="w-full text-xs text-gray-500 file:mr-4 file:py-2 file:px-4 file:rounded-lg file:border-0 file:bg-pink-50 file:text-pink-700 cursor-pointer"/>
          </div>

          <button type="submit" :disabled="enviando" class="w-full bg-pink-600 hover:bg-pink-700 text-white font-semibold py-2.5 rounded-lg text-sm shadow transition disabled:bg-gray-300">
            {{ enviando ? 'Enviando arquivos...' : 'Publicar no Site' }}
          </button>

          <!-- Mensagens de Sucesso ou Erro -->
          <p v-if="msgSucesso" class="text-xs font-medium text-green-600 text-center mt-2">✅ {{ msgSucesso }}</p>
          <p v-if="msgErro" class="text-xs font-medium text-red-600 text-center mt-2">❌ {{ msgErro }}</p>
        </form>
      </div>
    </main>
  </div>
</template>

<script setup>
import { ref, onMounted, watch } from 'vue'

// 🔗 CONEXÃO COM O BACKEND DO GOOGLE CLOUD RUN
const API_URL = 'https://estetica-backend-k6e4vrjs3a-uc.a.run.app/api/procedimentos'

const abaAtiva = ref('publico')
const maiorDeIdade = ref(localStorage.getItem('18plus') === 'true')
const procedimentos = ref([])
const carregando = ref(true)
const revelados = ref([])

const form = ref({ titulo: '', tipo: 'ROSTO', textoExplicativo: '' })
const arquivoReal = ref(null) // Armazena o binário puro extraído
const enviando = ref(false)
const msgSucesso = ref('')
const msgErro = ref('')

// Captura o arquivo do input HTML de forma isolada e limpa
const aoSelecionarArquivo = (e) => {
  if (e.target.files && e.target.files.length > 0) {
    arquivoReal.value = e.target.files[0] //  CORREÇÃO: Adicione o [0] aqui!
  }
}

// Busca os dados salvos no banco de dados através da sua API Quarkus
const carregarDados = async () => {
  if (abaAtiva.value !== 'publico') return
  carregando.value = true
  try {
    const res = await fetch(API_URL)
    procedimentos.value = await res.json()
  } catch (err) {
    console.error('Erro ao buscar dados do Cloud Run:', err)
  } finally {
    carregando.value = false
  }
}

onMounted(carregarDados)
watch(abaAtiva, carregarDados)

const confirmarMaioridade = () => { localStorage.setItem('18plus', 'true'); maiorDeIdade.value = true }
const sairDoSite = () => { window.location.href = 'https://instagram.com' }

// Envia o relatório e a foto do Painel Administrativo para o Quarkus
const enviarProcedimento = async () => {
  enviando.value = true; msgSucesso.value = ''; msgErro.value = ''

  if (!arquivoReal.value) {
    msgErro.value = 'Por favor, selecione um arquivo de imagem válido.'
    enviando.value = false
    return
  }

  const fd = new FormData()
  fd.append('titulo', form.value.titulo)
  fd.append('tipo', form.value.tipo)
  fd.append('textoExplicativo', form.value.textoExplicativo)
  fd.append('arquivo', arquivoReal.value) // Injeta o binário puro mapeado

  try {
    const res = await fetch(API_URL, { method: 'POST', body: fd })
    if (!res.ok) throw new Error()
    msgSucesso.value = 'Publicado com sucesso!'
    form.value.titulo = ''; form.value.textoExplicativo = ''; arquivoReal.value = null
  } catch {
    msgErro.value = 'Erro ao enviar dados para o servidor do Google Cloud.'
  } finally {
    enviando.value = false
  }
}
</script>
