<template>
  <h2
    class="bg-[#044477] w-40 p-3 text-3xl text-center text-white rounded-lg my-5 mx-24"
  >
  <strong>Med</strong>gical
  </h2>
  <div class="container mx-auto p-6">
    <div class="flex flex-wrap -mx-3">
      <!-- First Section: File Upload Area, Text Input, and Submit Button -->
      <div class="w-full md:w-1/2 px-3">
        <div class="bg-gray-100 p-6 rounded-lg shadow-lg mb-6">
          <!-- Drag and Drop / Click to Upload Area -->
          <div
            @dragover.prevent="handleDragOver"
            @dragleave="handleDragLeave"
            @drop.prevent="handleFileDrop"
            @click="triggerFileSelect"
            :class="[
              'border-4 border-dashed rounded-lg h-48 flex flex-col justify-center items-center cursor-pointer',
              isDragActive ? 'border-[#093456]' : 'border-gray-300',
              'hover:bg-gray-50',
            ]"
          >
            <template v-if="file">
              <img
                src="https://upload.wikimedia.org/wikipedia/commons/8/87/PDF_file_icon.svg"
                alt="PDF Icon"
                class="h-12 mb-2"
              />
              <p class="text-gray-500 text-center">{{ file.name }}</p>
              <!-- Cancel Button to remove the uploaded file -->
              <button
                @click.stop="removeFile"
                class="mt-4 bg-red-500 text-white px-4 py-2 rounded-lg hover:bg-red-600 focus:outline-none"
              >
              Cancelar
              </button>
            </template>
            <template v-else>
              <p class="text-gray-500">Click or drag and drop to upload PDF </p>
            </template>
            <input
              ref="fileInput"
              type="file"
              accept="application/pdf"
              class="hidden"
              @change="handleFileSelect"
            />
          </div>

          <!-- Text Input Field -->
          <div class="mt-4">
            <label for="description" class="block text-gray-700 mb-2"
              >Descrição</label
            >
            <textarea
              id="description"
              v-model="description"
              type="text"
              rows="10"
              placeholder="Introduza ou cole a descrição..."
              class="w-full p-2 border border-gray-300 rounded-lg focus:outline-none focus:border-[#044477]"
            />
          </div>

          <!-- Submit Button -->
          <button
            @click="submitForm"
            :disabled="!file"
            class="mt-4 w-1/3 bg-[#044477] text-white py-2 rounded-lg hover:bg-[#044477] focus:outline-none disabled:bg-gray-500 disabled:cursor-not-allowed"
          >
          Enviar
          </button>
        </div>
      </div>

      <!-- Second Section: Jumbotron for Results -->
      <div class="w-full md:w-1/2 px-3">
        <div class="bg-white p-6 rounded-lg shadow-lg">
          <h2 class="text-xl font-bold">Resultados</h2>
          <hr class="mb-5"/>
          <div v-if="results">
            <p>{{ results }}</p>
          </div>
          <div v-else>
            <p class="text-gray-500">Sem resultado para exibir.</p>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref } from "vue";

// State for file and description
const file = ref(null);
const description = ref("");
const results = ref("");
const isDragActive = ref(false); // To track drag status

// Trigger file select dialog
const fileInput = ref(null);
const triggerFileSelect = () => {
  fileInput.value.click(); // Directly use the ref instead of querySelector
};

// Handle file drop
const handleFileDrop = (e) => {
  const droppedFile = e.dataTransfer.files[0];
  if (droppedFile && droppedFile.type === "application/pdf") {
    file.value = droppedFile;
    isDragActive.value = false; // Reset drag active state after drop
    console.log("File uploaded:", file.value);
  } else {
    alert("Por favor carregue APENAS um ficheiro PDF.");
  }
};

// Handle file selection via file dialog
const handleFileSelect = (e) => {
  const selectedFile = e.target.files[0];
  if (selectedFile && selectedFile.type === "application/pdf") {
    file.value = selectedFile;
    console.log("File selected:", file.value);
  } else {
    alert("Selecione um ficheiro PDF.");
  }
};

// Remove uploaded file
const removeFile = () => {
  file.value = null;
  console.log("File removed");
  window.location.reload();
};

// Handle form submission
const submitForm = () => {
  if (file.value || description.value) {
    results.value = `File "${file.value.name}" uploaded with description: "${description.value}"`;
  } else {
    alert("Please upload a PDF file and provide a description.");
  }
};

// Handle drag over
const handleDragOver = () => {
  isDragActive.value = true;
};

// Handle drag leave
const handleDragLeave = () => {
  isDragActive.value = false;
};
</script>

<style scoped>
/* You can add custom styles if needed */
</style>
