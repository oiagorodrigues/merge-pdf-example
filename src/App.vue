<template>
    <div id="app">
        <div class="merge-from-local-file">
            <!-- <label for="pdf">Choose one or more pdf files:</label>
            <input type="file" id="pdf" name="pdf" accept=".pdf" multiple /> -->
            <button @click="mergePdf">Merge PDF</button>
        </div>
    </div>
</template>

<script lang="ts">
    import { Component, Vue } from 'vue-property-decorator'
    import { PDFDocument } from 'pdf-lib'
    import download from 'downloadjs'

    @Component
    export default class App extends Vue {
        async mergePdf() {
            const url1 = 'https://pdf-lib.js.org/assets/with_update_sections.pdf'
            const fileA = await fetch(url1).then(res => res.arrayBuffer())

            const url2 = 'https://pdf-lib.js.org/assets/with_large_page_count.pdf'
            const fileB = await fetch(url2).then(res => res.arrayBuffer())

            const files = [fileA, fileB]

            const mergedPdf = await PDFDocument.create()
            for (const pdfBytes of files) {
                const pdf = await PDFDocument.load(pdfBytes)
                const copiedPages = await mergedPdf.copyPages(pdf, pdf.getPageIndices())
                copiedPages.forEach(page => {
                    mergedPdf.addPage(page)
                })
            }

            const pdfBytes = await mergedPdf.save()

            download(pdfBytes, 'pdf-lib_page_copying_example.pdf', 'application/pdf')
            
        }
    }
</script>

<style>
    #app {
        font-family: Avenir, Helvetica, Arial, sans-serif;
        -webkit-font-smoothing: antialiased;
        -moz-osx-font-smoothing: grayscale;
        display: flex;
        justify-content: center;
        align-items: center;
        color: #2c3e50;
        margin-top: 60px;
    }

    .merge-from-local-file label {
        display: block;
        margin-bottom: 0.5rem;
    }
</style>
