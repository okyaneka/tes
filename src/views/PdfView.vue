<script setup>
import { onMounted, ref } from "vue";
import { degrees, PDFDocument, rgb, StandardFonts } from "pdf-lib";

const configKonva = ref({
  width: 200,
  height: 200,
});
const configCircle = ref({
  x: 100,
  y: 100,
  radius: 70,
  fill: "red",
  stroke: "black",
  strokeWidth: 4,
});
const inputFile = ref();
const file = ref();

function loadFile(e) {
  file.value = e.target.files[0];
  loadPdf();
}

async function loadPdf() {
  const fileReader = new FileReader();

  fileReader.onload = async (e) => {
    const existingPdfBytes = e.target.result;
    console.log({ existingPdfBytes });

    const pdfDoc = await PDFDocument.load(existingPdfBytes);
    const helveticaFont = await pdfDoc.embedFont(StandardFonts.Helvetica);

    const pages = pdfDoc.getPages();

    const firstPage = pages[0];
    const { width, height } = firstPage.getSize();
    firstPage.drawText("This text was added with JavaScript!", {
      x: 5,
      y: height / 2 + 300,
      size: 50,
      font: helveticaFont,
      color: rgb(0.95, 0.1, 0.1),
      rotate: degrees(-45),
    });
    const secondPage = pages[1];
    const { width2, height2 } = secondPage.getSize();
    secondPage.drawText("KULOOKY!", {
      x: 5,
      y: height / 2 + 300,
      size: 50,
      font: helveticaFont,
      color: rgb(0.95, 0.1, 0.1),
      rotate: degrees(-45),
    });

    const pdfBytes = await pdfDoc.save();
    console.log(pdfBytes);

    const download = document.createElement("a");
    download.target = "download";
    download.href = URL.createObjectURL(
      new Blob([pdfBytes], { type: "application/pdf" })
    );
    download.click();

    // download(pdfBytes, "pdf-lib_modification_example.pdf", "application/pdf");
  };

  fileReader.readAsArrayBuffer(file.value);
}

function handleStageScrolling(e) {
  console.log(e);
}

function handleSave() {}

onMounted(() => {
  // setTimeout(() => {
  //   inputFile.value.click();
  // }, 300);
});
</script>

<template>
  <input
    ref="inputFile"
    class="sr-only"
    type="file"
    @change="loadFile($event)"
    accept="application/pdf"
  />

  <div class="p-4 w-full">
    <div class="m-4 p-4 rounded-md bg-red-100">
      <v-stage
        class="bg-white"
        :config="configKonva"
        @click="handleStageScrolling"
        @scroll="handleStageScrolling"
      >
        <v-layer>
          <v-circle :config="configCircle"></v-circle>
        </v-layer>
      </v-stage>
    </div>
  </div>

  <div class="mt-4 p-4">
    <button
      class="rounded-md border border-green-600 px-6 py-2 text-green-600 hover:bg-green-50"
      @click="inputFile.click()"
    >
      Muat dokumen
    </button>
    <span class="p-1"></span>
    <button
      class="rounded-md bg-green-600 px-6 py-2 text-white hover:bg-green-700"
      @click="handleSave"
    >
      Simpan
    </button>
  </div>
</template>
