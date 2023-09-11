<template>
  <header class="bg-blue-500 p-4">
    <div class="container mx-auto flex items-center justify-between">
      <div class="flex items-center space-x-4">
        <img
          src="/247-2471138_pharmacy-symbol-hd-png-download.png"
          alt="Pharmacy Logo"
          class="w-12 h-12 rounded-full"
        />
        <h1 class="text-white text-2xl font-semibold">
          M Pharmacy صيدليه د/مؤمن لطفي
        </h1>
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

    <div
      class="flex flex-col sm:flex-row items-center justify-between border p-2"
    >
      <input
        type="text"
        v-model="newDrugName"
        placeholder="Enter Drug Name"
        class="border border-black w-full sm:w-[200px] rounded py-1 px-3 mb-2 sm:mb-0"
      />
      <div class="flex items-center gap-3">
        <button
          @click="decrementNewDrugCount"
          class="bg-red-500 py-2 px-3 rounded-lg text-white font-extrabold text-xl"
        >
          -
        </button>
        <input
          type="number"
          v-model="newDrugCount"
          :min="0"
          placeholder="Enter Count"
          class="border border-black w-full sm:w-[70px] rounded py-1 px-3"
        />
        <button
          @click="incrementNewDrugCount"
          class="bg-red-500 py-2 px-3 rounded-lg text-white font-extrabold text-xl"
        >
          +
        </button>
      </div>
      <button
        @click="addNewDrug"
        class="bg-green-500 py-2 px-3 rounded-lg text-white font-extrabold text-xl mt-2 sm:mt-0"
      >
        Add Drug
      </button>
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
      newDrugName: "", // For inputting a new drug name
      newDrugCount: 0, // For inputting a new drug count
    };
  },
  methods: {
    generatePDF() {
      const pdf = new jsPDF();

      // Filter drugs with a value greater than 0
      const filteredDrugs = this.drugs.filter((drug) => drug.value > 0);
      pdf.text("M Pharmacy | Dr.Momen Lotfy", 14, 10);

      // Define the table headers
      const headers = [["Drug Name", "Value"]];

      // Extract data for the table from the filtered drugs
      const data = filteredDrugs.map((drug) => [drug.name, drug.value]);

      // Create the table with jsPDF-AutoTable
      pdf.autoTable({
        head: headers,
        body: data,
        startY: 15,
        // Initial y-coordinate for the table
      });
      const date = new Date();
      const options = {
        weekday: "short",
        year: "numeric",
        month: "short",
        day: "numeric",
      };
      const formattedDate = date.toLocaleDateString(undefined, options);
      pdf.save(`${formattedDate}.pdf`);
    },
    decrementValue(index) {
      if (this.drugs[index].value > 0) {
        this.drugs[index].value--;
      }
    },
    incrementValue(index) {
      this.drugs[index].value++;
    },
    decrementNewDrugCount() {
      if (this.newDrugCount > 0) {
        this.newDrugCount--;
      }
    },
    incrementNewDrugCount() {
      this.newDrugCount++;
    },

    addNewDrug() {
      if (this.newDrugName && this.newDrugCount >= 0) {
        // Create a new drug object and add it to the drugs array
        this.drugs.push({
          name: this.newDrugName,
          type: "text", // You can adjust the type as needed
          value: this.newDrugCount,
        });

        // Clear the input fields after adding a new drug
        this.newDrugName = "";
        this.newDrugCount = 0;
      }
    },
  },
};
</script>
