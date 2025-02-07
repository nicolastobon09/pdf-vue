<script setup lang="ts">
import { ref, onMounted } from 'vue';
import { PDFDocument } from 'pdf-lib';

// Declare a reactive variable to hold the PDF data URI
const pdfDataUri = ref<string | undefined>(undefined);

async function flattenForm() {
    const formUrl = 'https://s3.us-east-1.amazonaws.com/duoc-shared/matriculas/202828779-3646-CONTRATO.pdf?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Date=20250207T204615Z&X-Amz-SignedHeaders=host&X-Amz-Expires=600&X-Amz-Credential=AKIAU6DQN66S6QGYIBSL%2F20250207%2Fus-east-1%2Fs3%2Faws4_request&X-Amz-Signature=83b5883dbafb00331847f2db76dfddce69cff653ba84e57ad51cd216eb793339#toolbar=0';
    const formPdfBytes = await fetch(formUrl).then(res => res.arrayBuffer());

    const pdfDoc = await PDFDocument.load(formPdfBytes);
    const pdfCopy = await pdfDoc.copy();
    
    const pdfBlob = new Blob([await pdfCopy.save()], { type: 'application/pdf' });
    	
    return URL.createObjectURL(pdfBlob);
}

// Use onMounted to call the function when the component is mounted
onMounted(async () => {
    pdfDataUri.value = await flattenForm();
});
</script>

<template>
  <div>
    <div>Here is my data</div>
    <!-- Use v-if to ensure the iframe is only rendered when pdfDataUri is available -->
     <div class="container">
       <iframe v-if="pdfDataUri" id="pdf" :src="pdfDataUri" style="width: 100%; height: 100%;"></iframe>
     </div>
    <a id="link" target="_blank" type="button" :href="pdfDataUri" >Descargar</a> 
  </div>
</template>