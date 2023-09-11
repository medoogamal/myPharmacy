<template>
  <header class="bg-blue-500 p-4">
    <div class="container mx-auto flex items-center justify-between">
      <div class="flex items-center space-x-4">
        <img
          src="/247-2471138_pharmacy-symbol-hd-png-download.png"
          alt="Pharmacy Logo"
          class="w-12 h-12 rounded-full"
        />
        <h1 class="text-white text-2xl font-semibold">Your Pharmacy</h1>
      </div>
    </div>
  </header>

  <div>
    <div
      v-for="(drug, index) in drugs"
      :key="index"
      class="flex items-center justify-between border p-2"
    >
      <label :for="'drug' + index" class="block capitalize">{{
        drug.name
      }}</label>
      <div class="flex items-center gap-3">
        <button
          @click="decrementValue(index)"
          class="bg-red-500 py-2 px-3 rounded-lg text-white font-extrabold text-xl"
        >
          -
        </button>
        <input
          :type="drug.type"
          :name="'drug' + index"
          :id="'drug' + index"
          :min="0"
          v-model="drug.value"
          class="border border-black w-[70px] rounded py-1 px-3 block"
        />
        <button
          @click="incrementValue(index)"
          class="bg-red-500 py-2 px-3 rounded-lg text-white font-extrabold text-xl"
        >
          +
        </button>
      </div>
    </div>
    <button
      @click="generatePDF"
      class="m-6 bg-gray-300 py-2 px-4 rounded-xl hover:bg-gray-500 font-extrabold"
    >
      Generate PDF
    </button>
  </div>
</template>

<script>
import jsPDF from "jspdf";
import "jspdf-autotable";
import drugs from "./drugs.js";

export default {
  data() {
    return {
      drugs: drugs.drugs,
    };
  },
  methods: {
    generatePDF() {
      const pdf = new jsPDF();

      // Filter drugs with a value greater than 0
      const filteredDrugs = this.drugs.filter((drug) => drug.value > 0);

      // Define the table headers
      const headers = [["Drug Name", "Value"]];

      // Extract data for the table from the filtered drugs
      const data = filteredDrugs.map((drug) => [drug.name, drug.value]);

      // Create the table with jsPDF-AutoTable
      pdf.autoTable({
        head: headers,
        body: data,
        startY: 20, // Initial y-coordinate for the table
        margin: { top: 15 },
      });

      pdf.save("drugs.pdf");
    },
    decrementValue(index) {
      if (this.drugs[index].value > 0) {
        this.drugs[index].value--;
      }
    },
    incrementValue(index) {
      this.drugs[index].value++;
    },
  },
};
</script>
