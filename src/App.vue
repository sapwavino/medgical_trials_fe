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
              <p class="text-gray-500">Please, Upload the patient file</p>
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
              >Criteria Importance:</label
            >
            <textarea
              id="description"
              v-model="description"
              type="text"
              rows="10"
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
        <div class="bg-white p-6 rounded-2xl shadow-lg">
          <h2 class="text-2xl font-bold mb-4 flex items-center">
            Results
            <span
              class="ml-5 border border-[#044477] text-[#044477] p-1 rounded-full px-4 hover:text-white hover:bg-[#093456] text-xs cursor-pointer"
              @click="submitted = false"
              v-if="submitted"
              ><icon>✖︎</icon> Clear</span
            >
          </h2>
          <div v-if="submitted">
            <div v-if="results === '' ">Loading results...<div class="loader"></div></div>
            <section v-else>
              <ol class="mt-5">
                <li
                  v-for="(result, index) in results"
                  :key="index"
                  class="rounded-md shadow-md p-5"
                >
                  <div v-if="index === 'Claude'">
                    <p>
                      <strong>Claude Evaluation: </strong
                      ><span
                        :class="{
                          'bg-emerald-500 text-white p-1 text-xs text-center inline-block px-2 shadow-md capitalize font-semibold rounded-full mt-5':
                            result.evaluation === 'Good candidate',
                          'bg-red-500 text-white p-1 text-xs text-center inline-block px-2 shadow-md capitalize font-semibold rounded-full mt-5':
                            result.evaluation === 'Bad candidate',
                        }"
                        >{{ result.evaluation }}</span
                      >
                    </p>
                    <p><strong>Claude Reason: </strong>{{ result.reason }}</p>
                  </div>
                  <div v-else>
                    <p>
                      <strong>GPT Evaluation: </strong
                      ><span
                        :class="{
                          'bg-emerald-500 text-white p-1 text-xs text-center inline-block px-2 shadow-md capitalize font-semibold rounded-full mt-5':
                            result.evaluation === 'Good candidate',
                          'bg-red-500 text-white p-1 text-xs text-center inline-block px-2 shadow-md capitalize font-semibold rounded-full mt-5':
                            result.evaluation === 'Bad candidate',
                        }"
                        >{{ result.evaluation }}</span
                      >
                    </p>
                    <p><strong>GPT Reason: </strong>{{ result.reason }}</p>
                  </div>
                </li>
              </ol>
            </section>
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

const file = ref(null);
const description = ref("");
const results = ref("");
const isDragActive = ref(false);
const submitted = ref(false);

// Trigger file select dialog
const fileInput = ref(null);
const triggerFileSelect = () => {
  fileInput.value.click(); 
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

const removeFile = () => {
  file.value = null;
  console.log("File removed");
  window.location.reload();
};

// Handle form submission
const submitForm = () => {
  submitted.value = false;
  results.value = "";
  if (file.value || description.value) {
    if (file.value) {
      const url = "https://medeval-ed1201aeb3b4.herokuapp.com/evaluate";
      const formData = new FormData();
      formData.append("file", fileInput.value.files[0]);
      fetch(url, {
        method: "POST",
        body: formData,
      })
        .then((response) => response.json())
        .then((data) => {
          results.value = data;
        });
      submitted.value = true;
    }
    if (description.value) {
      const url = "https://medeval-ed1201aeb3b4.herokuapp.com/evaluate";
      fetch(url, {
        method: "POST",
        body: description.value,
      })
        .then((response) => response.json())
        .then((data) => {
          results.value = data;
        });
      submitted.value = true;
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

<style>
/* HTML: <div class="loader"></div> */
.loader {
  width: 60px;
  aspect-ratio: 4;
  --_g: no-repeat radial-gradient(circle closest-side,#000 90%,#0000);
  background: 
    var(--_g) 0%   50%,
    var(--_g) 50%  50%,
    var(--_g) 100% 50%;
  background-size: calc(100%/3) 100%;
  animation: l7 1s infinite linear;
}
@keyframes l7 {
    33%{background-size:calc(100%/3) 0%  ,calc(100%/3) 100%,calc(100%/3) 100%}
    50%{background-size:calc(100%/3) 100%,calc(100%/3) 0%  ,calc(100%/3) 100%}
    66%{background-size:calc(100%/3) 100%,calc(100%/3) 100%,calc(100%/3) 0%  }
}
</style>
