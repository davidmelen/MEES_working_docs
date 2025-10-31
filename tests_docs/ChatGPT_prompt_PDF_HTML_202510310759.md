# ====== CONFIGURATION VARIABLES ======
DOC_ID: {mees-ch1}  
PDF_FILE_NAME: {Domestic_Private_Rented_Property_Minimum_Standard_-_Landlord_Guidance_2020.pdf}  
HTML_FILE_NAME: {chapter_1.html}  
PAGE_RANGE: {14–34}   # inclusive page range to extract text from

---

## ROLE
You are a software developer preparing content for a **Retrieval-Augmented Generation (RAG)** chatbot.

---

## CONTEXT
You need to generate a **semantically structured HTML document** that will later be chunked and indexed by a RAG pipeline.

Details:
- The **source** is the PDF file `{PDF_FILE_NAME}`.  
- Extract **only** the text content from pages `{PAGE_RANGE}`.  
- The **output file name** must be `{HTML_FILE_NAME}`.  
- The extracted text must be **identical** to the source — do **not** summarize, rephrase, or modify wording.  
- The **HTML structure** should reflect the document’s natural hierarchy (headings, subheadings, lists, notes, etc.).  
- The HTML must be **optimized for recursive and semantic chunking** using Python libraries such as:
  - `langchain_text_splitters` (for `HTMLHeaderTextSplitter` and `RecursiveCharacterTextSplitter`)
  - `BeautifulSoup` (for structural traversal)

---

## TASK
Generate a well-formed HTML document as follows:

1. **Extract Text**
   - Parse and extract all text from pages `{PAGE_RANGE}` of the PDF `{PDF_FILE_NAME}`.  
   - Preserve all headings, subheadings, paragraph order, numbered lists, and note boxes.

2. **Construct HTML**
   - Use clear, semantic HTML tags: `<article>`, `<section>`, `<header>`, `<h1>`–`<h4>`, `<p>`, `<ul>`, `<li>`, `<aside>`, `<figure>`, `<table>`, etc.
   - Use `data-chunk`, `data-level`, and `data-source-page` attributes to mark logical and page boundaries.  
   - Ensure nested structure reflects the logical hierarchy of the source (e.g., chapter → section → subsection → note).  
   - Add an `<article>` root element with `id="{DOC_ID}"`.

3. **Metadata and Structure**
   - Include `<meta>` tags for `charset`, `viewport`, and `description`.  
   - The `<title>` should clearly name the chapter or content range (e.g., “Chapter 1: Pages 14–34”).  
   - Use comments or data attributes to trace page provenance (e.g., `data-source-page="22"`).

4. **Output**
   - Output the full HTML document for `{HTML_FILE_NAME}`.  
   - The file must be self-contained, valid HTML5, and ready for parsing by RAG chunking scripts.  
   - Do **not** include any text not present in the PDF.  

---

## EXAMPLE STRUCTURAL GUIDELINES
```html
<article id="{DOC_ID}" data-chunk="chapter">
  <header>
    <h1>Chapter 1: How the Regulations Apply to Domestic Property</h1>
  </header>

  <section id="ch1-1" data-chunk="section" data-level="1.1">
    <h2>1.1 Domestic Private Rented Sector (PRS) Scope</h2>

    <section id="ch1-1-1" data-chunk="subsection" data-level="1.1.1">
      <h3>1.1.1 Properties Covered by the Minimum Level of Energy Efficiency Provisions</h3>
      <p>[text extracted verbatim from PDF]</p>

      <aside role="note" id="note-licence-vs-tenancy">
        <h4>Licence vs Tenancy</h4>
        <p>[note text verbatim]</p>
      </aside>
    </section>
  </section>
</article>

