<template>
    <div class="container mx-auto p-4">
        <h1 class="text-2xl font-semibold mb-4">CSV Uploader</h1>
        <input type="file" ref="fileInput" accept=".csv" @change="handleFileUpload" class="mb-4" />
        <div class="my-4">
            <CsvTable :csvData="csvData" v-if="csvData" />
        </div>
        <p v-if="uploadError" class="text-red-500">{{ uploadError }}</p>
    </div>
</template>
  
<script>
import CsvTable from "@/components/CsvTable.vue";

export default {
    name: 'MyUploader',
    components: {
        CsvTable,
    },
    data() {
        return {
            csvData: null,
            uploadError: null,
        };
    },
    methods: {
        handleFileUpload(event) {
            const file = event.target.files[0];
            if (file) {
                if (file.name.endsWith(".csv")) {
                    const reader = new FileReader();
                    reader.onload = (e) => {
                        this.csvData = e.target.result;
                        this.uploadError = null;
                    };
                    reader.readAsText(file);
                } else {
                    this.uploadError = "Please select a valid CSV file. It must to have .csv extension.";
                }
            }
        },
    },
};
</script>
