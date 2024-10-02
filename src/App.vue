<template>
  <div>
    <h2
      class="bg-[#044477] w-40 p-3 text-3xl text-center text-white rounded-lg my-5 mx-24 shadow-2xl"
    >
      <strong>Med</strong>gical
    </h2>
  </div>

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
              'border-[3px] border-dashed rounded-lg h-48 flex flex-col justify-center items-center cursor-pointer',
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
                Cancel
              </button>
            </template>
            <template v-else>
              <p class="text-gray-500">Click or Drag and Drop to Upload PDF</p>
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
              >Description</label
            >
            <textarea
              id="description"
              v-model="description"
              type="text"
              rows="10"
              placeholder="Type or paste description..."
              class="w-full p-2 border border-gray-300 rounded-lg focus:outline-none focus:border-[#044477]"
            />
          </div>

          <!-- Submit Button -->
          <button
            @click="submitForm"
            :disabled="!file && !description"
            class="mt-4 w-1/3 bg-[#044477] text-white py-2 rounded-lg hover:bg-[#093456] focus:outline-none disabled:bg-gray-500 disabled:cursor-not-allowed"
          >
            Submit
          </button>
        </div>
      </div>

      <!-- Second Section: Jumbotron for Results -->
      <div class="w-full md:w-1/2 px-3">
        <div class="bg-white p-6 rounded-lg shadow-lg">
          <h2 class="text-2xl font-bold mb-4">Results <span class="ml-5 border border-[#044477] text-[#044477] p-1 rounded-full px-4 hover:text-white hover:bg-[#093456] text-xs cursor-pointer" @click="submitted = false" v-if="submitted">Clear</span></h2>
          <div v-if="submitted">

            <p class="bg-green-500 text-white p-1 text-xs text-center w-[20%] capitalize font-semibold rounded-full mt-5" >{{ dummyResponse.evaluation }}</p>
            <ol class="mt-5">
              <li v-for="(item, index) in dummyResponse.reasons" :key="index" class="rounded-md shadow-md p-5">
                <p><strong>GPT Evaluation: </strong>{{ item.GPT.evaluation }}</p>
                <p><strong>GPT Reason: </strong>{{ item.GPT.reason }}</p>
                <hr class="my-2"/>
                <p><strong>Claude Evaluation: </strong>{{ item.claude.evaluation }}</p>
                <p><strong>Claude Reason: </strong>{{ item.claude.reason }}</p>
              </li>
            </ol>
          </div>
          <div v-else>
            <p class="text-gray-500">No results to display.</p>
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
const isDragActive = ref(false); 
const submitted = ref(false); 
const dummyResponse = ref({
  "evaluation": "good candidate",
  "reasons":[
    {
      "GPT": {
        "evaluation": "Good candidate",
        "reason": "João Silva is a good candidate for the medical trials as he has extensive experience in clinical research and a strong adherence to ethical guidelines.",
      },
      "claude": {
        "evaluation": "Good candidate",
        "reason": "João has extensive education and experience in clinical research and a strong adherence to ethical guidelines."
      }
    }
  ]
})

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
    alert("Please upload a PDF file.");
  }
};

// Handle file selection via file dialog
const handleFileSelect = (e) => {
  const selectedFile = e.target.files[0];
  if (selectedFile && selectedFile.type === "application/pdf") {
    file.value = selectedFile;
    console.log("File selected:", file.value);
  } else {
    alert("Please select a PDF file.");
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
  submitted.value = false;
  if (file.value || description.value) {
    if (file.value) {
      results.value = `File "${file.value.name}" uploaded"`;
      submitted.value = true
    }
    if (description.value) {
      results.value = `Description: "${description.value}"`;
      submitted.value = true
    }
    file.value = null;
    description.value = "";
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
