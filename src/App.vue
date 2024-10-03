<template>
  <div>
    <h2
      class="bg-[#044477] w-40 p-2 text-2xl text-center text-white rounded-lg my-5 mx-24 shadow-2xl"
    >
      <strong>Med</strong>gical
    </h2>
  </div>

  <div class="text-center text-white text-4xl font-bold">
    Medical Trials Classifier
  </div>

  <div class="container mx-auto p-6">
    <div class="flex flex-wrap -mx-3">
      <!-- First Section: Trial File Upload -->
      <div
        class="w-full md:w-1/3 mx-auto px-3"
        v-if="!trialFileUploaded && currentScreen === 1"
      >
        <div class="bg-gray-100 p-6 rounded-lg shadow-lg mb-6">
          <div
            @dragover.prevent="handleDragOver"
            @dragleave="handleDragLeave"
            @drop.prevent="handleTrialFileDrop"
            @click="triggerTrialFileSelect"
            :class="[
              'border-[3px] border-dashed rounded-lg h-48 flex flex-col justify-center items-center cursor-pointer',
              isDragActive ? 'border-[#093456]' : 'border-gray-300',
              'hover:bg-white',
            ]"
          >
            <template v-if="trialFile">
              <img
                src="https://upload.wikimedia.org/wikipedia/commons/8/87/PDF_file_icon.svg"
                alt="PDF Icon"
                class="h-12 mb-2"
              />
              <p class="text-gray-500 text-center" v-if="trialFile">
                {{ trialFile.name }}
              </p>
              <!-- Cancel Button to remove the uploaded file -->
              <button
                @click.stop="removeTrialFile"
                class="mt-4 bg-red-700 text-white px-4 py-2 rounded-lg hover:bg-red-600 focus:outline-none text-xs transform duration-150 ease-in-out hover:scale-105"
              >
                ✖︎ Remove File
              </button>
            </template>
            <template v-else>
              <svg
                xmlns="http://www.w3.org/2000/svg"
                shape-rendering="geometricPrecision"
                text-rendering="geometricPrecision"
                image-rendering="optimizeQuality"
                viewBox="0 0 441 512.02"
                class="h-1/3"
              >
                <path
                  d="M324.87 279.77c32.01 0 61.01 13.01 82.03 34.02 21.09 21 34.1 50.05 34.1 82.1 0 32.06-13.01 61.11-34.02 82.11l-1.32 1.22c-20.92 20.29-49.41 32.8-80.79 32.8-32.06 0-61.1-13.01-82.1-34.02-21.01-21-34.02-50.05-34.02-82.11s13.01-61.1 34.02-82.1c21-21.01 50.04-34.02 82.1-34.02zM243.11 38.08v54.18c.99 12.93 5.5 23.09 13.42 29.85 8.2 7.01 20.46 10.94 36.69 11.23l37.92-.04-88.03-95.22zm91.21 120.49-41.3-.04c-22.49-.35-40.21-6.4-52.9-17.24-13.23-11.31-20.68-27.35-22.19-47.23l-.11-1.74V25.29H62.87c-10.34 0-19.75 4.23-26.55 11.03-6.8 6.8-11.03 16.21-11.03 26.55v336.49c0 10.3 4.25 19.71 11.06 26.52 6.8 6.8 16.22 11.05 26.52 11.05h119.41c2.54 8.79 5.87 17.25 9.92 25.29H62.87c-17.28 0-33.02-7.08-44.41-18.46C7.08 432.37 0 416.64 0 399.36V62.87c0-17.26 7.08-32.98 18.45-44.36C29.89 7.08 45.61 0 62.87 0h173.88c4.11 0 7.76 1.96 10.07 5l109.39 118.34c2.24 2.43 3.34 5.49 3.34 8.55l.03 119.72c-8.18-1.97-16.62-3.25-25.26-3.79v-89.25zm-229.76 54.49c-6.98 0-12.64-5.66-12.64-12.64 0-6.99 5.66-12.65 12.64-12.65h150.49c6.98 0 12.65 5.66 12.65 12.65 0 6.98-5.67 12.64-12.65 12.64H104.56zm0 72.3c-6.98 0-12.64-5.66-12.64-12.65 0-6.98 5.66-12.64 12.64-12.64h142.52c3.71 0 7.05 1.6 9.37 4.15a149.03 149.03 0 0 0-30.54 21.14H104.56zm0 72.3c-6.98 0-12.64-5.66-12.64-12.65 0-6.98 5.66-12.64 12.64-12.64h86.2c-3.82 8.05-6.95 16.51-9.29 25.29h-76.91zm264.81 47.03c3.56-.14 6.09-1.33 7.54-3.55 3.98-5.94-1.44-11.81-5.19-15.94l-40.04-40.71c-4.32-4.26-9.32-4.31-13.64 0l-41.01 41.82c-3.51 3.96-7.86 9.36-4.19 14.83 1.49 2.22 4 3.41 7.56 3.55h19.74v30.69c0 5.84 4.81 10.69 10.7 10.69h28.06c5.9 0 10.71-4.81 10.71-10.69v-30.69h19.76z"
                />
              </svg>
              <p class="text-gray-500">Upload the Trial PDF File</p>
            </template>
            <input
              ref="trialFileInput"
              type="file"
              accept="application/pdf"
              class="hidden"
              @change="handleTrialFileSelect"
            />
          </div>

          <!-- Submit Button to go to second screen -->
          <button
            @click="goToNextScreen"
            :disabled="!trialFile"
            class="mt-4 w-1/3 bg-[#044477] text-white py-2 rounded-xl hover:bg-[#093456] focus:outline-none disabled:bg-gray-500 disabled:cursor-not-allowed"
          >
            Continue →
          </button>
        </div>
      </div>

      <!-- Second Section: Patient File Upload -->
      <div v-else class="px-3 grid grid-cols-2 w-full">
        <p class="text-gray-200 text-center col-span-2 mb-5 text-2xl">
          <strong v-if="trialFile">Trial File: </strong>{{ trialFile.name }}
        </p>
        <div class="bg-gray-100 p-6 rounded-lg shadow-lg mb-6">
          <div
            @dragover.prevent="handleDragOver"
            @dragleave="handleDragLeave"
            @drop.prevent="handlePatientFileDrop"
            @click="triggerPatientFileSelect"
            :class="[
              'border-[3px] border-dashed rounded-lg h-48 flex flex-col justify-center items-center cursor-pointer',
              isDragActive ? 'border-[#093456]' : 'border-gray-300',
              'hover:bg-gray-50',
            ]"
          >
            <template v-if="patientFile">
              <img
                src="https://upload.wikimedia.org/wikipedia/commons/8/87/PDF_file_icon.svg"
                alt="PDF Icon"
                class="h-12 mb-2"
              />
              <p class="text-gray-500 text-center" v-if="patientFile">
                {{ patientFile.name }}
              </p>
              <button
                @click.stop="removePatientFile"
                class="mt-4 bg-red-500 text-white px-4 py-2 rounded-lg hover:bg-red-600 focus:outline-none text-xs"
              >
                ✖︎ Remove Patient File
              </button>
            </template>
            <template v-else>
              <svg
                xmlns="http://www.w3.org/2000/svg"
                shape-rendering="geometricPrecision"
                text-rendering="geometricPrecision"
                image-rendering="optimizeQuality"
                viewBox="0 0 441 512.02"
                class="h-1/3"
              >
                <path
                  d="M324.87 279.77c32.01 0 61.01 13.01 82.03 34.02 21.09 21 34.1 50.05 34.1 82.1 0 32.06-13.01 61.11-34.02 82.11l-1.32 1.22c-20.92 20.29-49.41 32.8-80.79 32.8-32.06 0-61.1-13.01-82.1-34.02-21.01-21-34.02-50.05-34.02-82.11s13.01-61.1 34.02-82.1c21-21.01 50.04-34.02 82.1-34.02zM243.11 38.08v54.18c.99 12.93 5.5 23.09 13.42 29.85 8.2 7.01 20.46 10.94 36.69 11.23l37.92-.04-88.03-95.22zm91.21 120.49-41.3-.04c-22.49-.35-40.21-6.4-52.9-17.24-13.23-11.31-20.68-27.35-22.19-47.23l-.11-1.74V25.29H62.87c-10.34 0-19.75 4.23-26.55 11.03-6.8 6.8-11.03 16.21-11.03 26.55v336.49c0 10.3 4.25 19.71 11.06 26.52 6.8 6.8 16.22 11.05 26.52 11.05h119.41c2.54 8.79 5.87 17.25 9.92 25.29H62.87c-17.28 0-33.02-7.08-44.41-18.46C7.08 432.37 0 416.64 0 399.36V62.87c0-17.26 7.08-32.98 18.45-44.36C29.89 7.08 45.61 0 62.87 0h173.88c4.11 0 7.76 1.96 10.07 5l109.39 118.34c2.24 2.43 3.34 5.49 3.34 8.55l.03 119.72c-8.18-1.97-16.62-3.25-25.26-3.79v-89.25zm-229.76 54.49c-6.98 0-12.64-5.66-12.64-12.64 0-6.99 5.66-12.65 12.64-12.65h150.49c6.98 0 12.65 5.66 12.65 12.65 0 6.98-5.67 12.64-12.65 12.64H104.56zm0 72.3c-6.98 0-12.64-5.66-12.64-12.65 0-6.98 5.66-12.64 12.64-12.64h142.52c3.71 0 7.05 1.6 9.37 4.15a149.03 149.03 0 0 0-30.54 21.14H104.56zm0 72.3c-6.98 0-12.64-5.66-12.64-12.65 0-6.98 5.66-12.64 12.64-12.64h86.2c-3.82 8.05-6.95 16.51-9.29 25.29h-76.91zm264.81 47.03c3.56-.14 6.09-1.33 7.54-3.55 3.98-5.94-1.44-11.81-5.19-15.94l-40.04-40.71c-4.32-4.26-9.32-4.31-13.64 0l-41.01 41.82c-3.51 3.96-7.86 9.36-4.19 14.83 1.49 2.22 4 3.41 7.56 3.55h19.74v30.69c0 5.84 4.81 10.69 10.7 10.69h28.06c5.9 0 10.71-4.81 10.71-10.69v-30.69h19.76z"
                />
              </svg>
              <p class="text-gray-500">Upload Patient PDF File</p>
            </template>
            <input
              ref="patientFileInput"
              type="file"
              accept="application/pdf"
              class="hidden"
              @change="handlePatientFileSelect"
            />
          </div>

          <!-- Additional options and submission -->
          <div class="mt-4">
            <label for="description" class="block text-gray-700 mb-2"
              >Criteria Importance:</label
            >
            <textarea
              id="description"
              v-model="description"
              rows="5"
              placeholder="List out your criteria weights"
              class="w-full p-2 border border-gray-300 rounded-lg focus:outline-none focus:border-[#044477]"
            />
          </div>

          <button
            @click="goBack"
            class="mt-4 ml-2 px-3 rounded-xl mx-2 bg-[#044477] text-white py-2 hover:bg-gray-600 focus:outline-none"
          >
            ← Start over
          </button>

          <button
            @click="submitForm"
            :disabled="!trialFile || (!patientFile && !description)"
            class="mt-4 mx-2 px-3 bg-emerald-600 text-white py-2 rounded-xl hover:bg-[#093456] focus:outline-none disabled:bg-gray-500 disabled:cursor-not-allowed"
          >
          Submit ✔︎ 
          </button>
        </div>
        <div class="px-3">
          <div class="bg-white p-6 rounded-2xl shadow-lg">
            <h2 class="text-2xl font-bold mb-4 flex items-center">
              Results
              <span
                class="ml-5 border border-[#044477] text-[#044477] p-1 rounded-full px-4 hover:text-white hover:bg-[#093456] text-xs cursor-pointer"
                @click="
                  submitted = false;
                  removePatientFile();
                  results = null;
                  patientFileInput = null;
                "
                v-if="submitted && results !== null"
                ><icon>✖︎</icon> Clear</span
              >
            </h2>
            <div v-if="results === null">
              <p class="text-gray-500" v-if="!submitted">
                No results to display.
              </p>
              <section v-else>
                Loading results...
                <div class="loader"></div>
              </section>
            </div>
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
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, watch } from "vue";

const trialFile = ref(null);
const patientFile = ref(null);
let currentScreen = ref(1);
const description = ref("");
const isDragActive = ref(false);
const trialFileUploaded = ref(false);
const submitted = ref(false);
const results = ref(null);
const trialFileInput = ref(null);
const patientFileInput = ref(null);

const triggerTrialFileSelect = () => trialFileInput.value.click();

const handleTrialFileSelect = (e) => {
  const selectedFile = e.target.files[0];
  if (selectedFile && selectedFile.type === "application/pdf") {
    trialFile.value = selectedFile;
  } else {
    alert("Please select a PDF file.");
  }
};

const handleTrialFileDrop = (e) => {
  const droppedFile = e.dataTransfer.files[0];
  if (droppedFile && droppedFile.type === "application/pdf") {
    trialFile.value = droppedFile;
    isDragActive.value = false;
  } else {
    alert("Please upload a PDF file.");
  }
};

const removeTrialFile = () => {
  trialFile.value = null;
  trialFile.value.value = "";
  trialFileUploaded.value = false;
};

const goToNextScreen = () => {
  trialFileUploaded.value = true;
};

const goBack = () => {
  trialFileUploaded.value = false;
  trialFile.value.value = null;
  trialFile.value = null;
  patientFileFileUploaded.value = false;
  patientFile.value.value = null;
  patientFile.value = null;
  results.value = null;
  currentScreen.value = 1;
};

const triggerPatientFileSelect = () => patientFileInput.value.click();

const handlePatientFileSelect = (e) => {
  const selectedFile = e.target.files[0];
  if (selectedFile && selectedFile.type === "application/pdf") {
    patientFile.value = selectedFile;
  } else {
    alert("Please select a PDF file.");
  }
};

const handlePatientFileDrop = (e) => {
  const droppedFile = e.dataTransfer.files[0];
  if (droppedFile && droppedFile.type === "application/pdf") {
    patientFile.value = droppedFile;
    isDragActive.value = false;
  } else {
    alert("Please upload a PDF file.");
  }
};

const removePatientFile = () => {
  patientFile.value.value = "";
  patientFile.value = null;
};

// Submit form
const submitForm = () => {
  submitted.value = true;
  results.value = null;
  if (trialFile.value || patientFile.value || description.value) {
    const url = "https://medeval-ed1201aeb3b4.herokuapp.com/evaluate";
    const formData = new FormData();
    formData.append("trialsPDF", trialFile.value);
    formData.append("patientPDF", patientFile.value);
    formData.append("text", description.value);
    fetch(url, {
      method: "POST",
      body: formData,
    })
      .then((response) => response.json())
      .then((data) => {
        submitted.value = false;
        console.log(data);
        results.value = data;
        submitted.value = true;
      });
  } else {
    alert("Please upload a PDF.");
  }
};

const handleDragOver = () => {
  isDragActive.value = true;
};

const handleDragLeave = () => {
  isDragActive.value = false;
};
</script>

<style>
/* HTML: <div class="loader"></div> */
.loader {
  width: 60px;
  aspect-ratio: 4;
  --_g: no-repeat radial-gradient(circle closest-side, #000 90%, #0000);
  background: var(--_g) 0% 50%, var(--_g) 50% 50%, var(--_g) 100% 50%;
  background-size: calc(100% / 3) 100%;
  animation: l7 1s infinite linear;
}
@keyframes l7 {
  33% {
    background-size: calc(100% / 3) 0%, calc(100% / 3) 100%, calc(100% / 3) 100%;
  }
  50% {
    background-size: calc(100% / 3) 100%, calc(100% / 3) 0%, calc(100% / 3) 100%;
  }
  66% {
    background-size: calc(100% / 3) 100%, calc(100% / 3) 100%, calc(100% / 3) 0%;
  }
}
</style>