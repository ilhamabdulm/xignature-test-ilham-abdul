<script setup>
import { onMounted, reactive, ref, watch } from 'vue';
import pdfjsLib from 'pdfjs-dist/build/pdf';
import {
  PDFPageView,
  EventBus,
  PDFLinkService,
  PDFFindController,
} from 'pdfjs-dist/web/pdf_viewer';
import 'pdfjs-dist/web/pdf_viewer.css';

import PDFNavigation from './PdfNavigation.vue';

pdfjsLib.GlobalWorkerOptions.workerSrc =
  'https://cdn.jsdelivr.net/npm/pdfjs-dist@2.0.943/build/pdf.worker.min.js';

const totalPages = ref(1);

const state = reactive({
  page: 1,
  pdfFile: null,
  scale: 1.5,
  docPath:
    'https://raw.githubusercontent.com/mozilla/pdf.js/ba2edeae/web/compressed.tracemonkey-pldi-09.pdf',
  container: null,
  eventBus: null,
  pageRendering: false,
  pagePending: null,
  linkService: null,
  pdfFindController: null,
});

function onNextPage() {
  if (state.page >= totalPages.value) return;
  else state.page++;
}

function onPrevPage() {
  if (state.page <= 1) return;
  state.page--;
}

async function setPdfView(pdf, page) {
  state.container.innerHTML = '';

  const perPage = await pdf.getPage(page);
  const pdfViewer = new PDFPageView({
    container: state.container,
    id: page,
    defaultViewport: perPage.getViewport({ scale: state.scale }),
    scale: state.scale,
    eventBus: state.eventBus,
    linkService: state.linkService,
    findController: state.pdfFindController,
  });
  state.linkService.setViewer(pdfViewer);

  pdfViewer.setPdfPage(perPage);
  pdfViewer.draw();
}

async function getPdf() {
  const container = document.getElementById('pageContainer');
  const pdf = await pdfjsLib.getDocument({
    url: state.docPath,
  });
  const eventBus = new EventBus();
  const pdfLinkService = new PDFLinkService({
    eventBus,
  });
  const pdfFindController = new PDFFindController({
    eventBus,
    linkService: pdfLinkService,
  });
  const _totalPages = pdf.numPages;

  state.pdfFile = pdf;
  state.container = container;
  state.eventBus = eventBus;
  state.linkService = pdfLinkService;
  state.pdfFindController = pdfFindController;
  totalPages.value = _totalPages;

  setPdfView(pdf, 1, 1.5);
}

onMounted(() => {
  getPdf();
});

watch(
  () => state.page,
  (page) => {
    setPdfView(state.pdfFile, page, 1.5);
  }
);
</script>

<template>
  <main class="flex flex-col items-center justify-center w-full">
    <PDFNavigation
      :current-page="state.page"
      :total-pages="totalPages"
      @on-next="onNextPage"
      @on-prev="onPrevPage"
    />

    <div id="pageContainer" class="flex justify-center flex-wrap"></div>
  </main>
</template>

<style></style>
