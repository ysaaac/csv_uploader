<template>
    <div :class="{
        'bg-yellow-200 text-yellow-800 border border-yellow-500': processStatus === 'Process initialized',
        'bg-green-200 text-green-800 border border-green-500': processStatus === 'Process finished',
        'rounded-md p-2 m-4': true
    }">
        <div class="flex items-center justify-center">
            <p class="font-semibold text-sm">{{ processStatus }}: The CSV data is being uploaded. Status will be shown at
                the table.</p>
        </div>
    </div>
    <div class="w-full h-64 overflow-x-auto min-h-screen">
        <table class="min-w-full">
            <thead>
                <tr>
                    <th class="px-6 py-3 bg-gray-50 text-left text-xs leading-4 font-medium text-gray-500 uppercase tracking-wider"
                        v-for="(header, index) in tableHeaders" :key="index">
                        {{ header }}
                    </th>
                </tr>
            </thead>
            <tbody>
                <tr v-for="(row, rowIndex) in parsedCsvData" :key="rowIndex">
                    <td v-for="(value, colIndex) in row" :key="colIndex"
                        class="px-6 py-4 whitespace-no-wrap text-sm leading-5 text-gray-900">
                        <div v-if="colIndex === 3" v-html="value"></div>
                        <div v-else>{{ value }}</div>
                    </td>
                </tr>
            </tbody>
        </table>
    </div>
</template>
  
<script>
import { ref, onMounted } from 'vue';

export default {
    name: 'CsvTable',
    props: {
        csvData: String,
    },
    setup(props) {
        const tableHeaders = ref(['Name', 'Phone', 'Email', 'Status']);
        const endpointURI = 'https://8j5baasof2.execute-api.us-west-2.amazonaws.com/production/tests/trucode/items'
        const processStatus = ref('Process initialized')
        const parsedCsvData = ref(props.csvData.trim().split('\n').map((line) => {
            let [name, phone, email] = line.split(',');
            phone = phone.replace(/(\d{3})(\d{3})(\d{4})/, '$1-$2-$3')
            return [name, phone, email, '⚠️ Pending']
        }));

        onMounted(async () => {
            for (const value of parsedCsvData.value) {
                const [name, phone, email] = value;
                try {
                    const response = await fetch(endpointURI, {
                        method: 'POST',
                        headers: {
                            'Content-Type': 'application/json',
                        },
                        body: JSON.stringify({ name, email, phone }),
                    });

                    if (!response.ok) {
                        const errorResponse = await response.json();
                        value[3] = `❌ Error </br> ${errorResponse.error}`
                        throw new Error(response.body);
                    }

                    await response.json();
                    value[3] = '✅ Success';
                } catch (error) {
                    console.error('Error:', error);
                }
            }
            processStatus.value = 'Process finished'
        });

        return {
            tableHeaders,
            parsedCsvData,
            processStatus
        };
    },

};
</script>