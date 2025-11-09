# Italian Text Complexity Analyser

This is a web application built with [Streamlit](https://streamlit.io/) and [spaCy](https://spacy.io/) designed to analyse the linguistic complexity of Italian texts.

It provides both high-level readability scores and a detailed, colour-coded breakdown of specific linguistic features that contribute to a text's complexity, making it an excellent tool for editors, writers, and public administration officials aiming for clearer communication.

## Features

This tool analyses a given Italian text and provides two main types of feedback:

### 1. Quantitative Readability Metrics

* **Gulpease Index:** The standard readability score for the Italian language.
* **Corrected Type-Token Ratio (CTTR):** A measure of lexical diversity (how varied the vocabulary is), corrected for text length.
* **Average Sentence Length:** The average number of words per sentence (filters out very short sentences for accuracy).

### 2. Linguistic Feature Highlighting

The app processes the text and highlights specific structures known to increase cognitive load for the reader.



A legend is provided in the app, which includes:

* <span style="border-bottom: 2px dashed red">Long Sentences (&gt;25 tokens)</span>
* <span style="background-color: yellow">Gerunds</span> (e.g., *facendo*, *andando*)
* <span style="background-color: lightgreen">Technical Terms/Jargon</span> (from a custom list)
* <span style="background-color: lightgray">Archaisms</span> (e.g., *siffatto*, *altresì*)
* <span style="background-color: lightsalmon">Enclitics</span> (e.g., *dicasi*, *trattasi*)
* <span style="background-color: lavender">Formal Conjunctions</span>
* <span style="text-decoration: underline blue">Subordinate Clauses</span>
* <span style="background-color: #ffd580">Passive Voice</span>
* <span style="background-color: #b3e0ff">Postposed Subjects</span> (Subject-Verb inversion)

---

## Tech Stack

* **Frontend:** [Streamlit](https://streamlit.io/)
* **NLP Core:** [spaCy](https://spacy.io/)
* **Language Model:** `it_core_news_lg` (Italian large model)
* **Pattern Matching:** `re` (Regex) for preprocessing, `spaCy Matcher` for complex phrases.

---

## How It Works

1.  **Preprocessing:** The input text is first cleaned by a powerful, custom regex function (`text_preprocessing`) that normalises dozens of common Italian administrative and legal abbreviations (e.g., `d.lgs.` -> `dlgs`, `art.` -> `art`).
2.  **NLP Processing:** The cleaned text is passed to the `spaCy` pipeline, which is cached using `@st.cache_resource`. This tokenises the text and performs part-of-speech (POS) tagging, dependency parsing, and lemmatisation.
3.  **Metrics Calculation:** The app calculates the **Gulpease Index**, **CTTR**, and **Average Sentence Length** from the spaCy `Doc` object.
4.  **Feature Extraction:** The core function (`locate_complexity`) iterates through every sentence and token, using spaCy's linguistic annotations (`token.pos_`, `token.dep_`, `token.morph`) and custom word lists to identify complex features.
5.  **HTML Rendering:** The function builds an HTML string in real-time. It maps CSS styles to character indices in a dictionary. This allows multiple highlights to overlap (e.g., a word can be *both* part of a long sentence and a technical term). The final HTML is rendered in Streamlit using `st.markdown(unsafe_allow_html=True)`.

---

## Setup and Launch

This app is designed to be run locally or deployed via Streamlit Community Cloud.

### 1. Running Locally (Recommended)

1.  **Clone the repository:**
    ```bash
    git clone [https://github.com/your-username/your-repo.git](https://github.com/your-username/your-repo.git)
    cd your-repo
    ```

2.  **Create a virtual environment** (recommended):
    ```bash
    python -m venv venv
    source venv/bin/activate  # On Windows: venv\Scripts\activate
    ```

3.  **Install Python dependencies:** Create a `requirements.txt` file in the repository root:
    ```txt
    streamlit
    spacy
    ```
    Then, install them:
    ```bash
    pip install -r requirements.txt
    ```

4.  **Download the spaCy Model:** The script requires the Italian large model. Download it via the command line:
    ```bash
    python -m spacy download it_core_news_lg
    ```

5.  **Run the app:**
    ```bash
    streamlit run new_app.py
    ```

### 2. Deploying to Streamlit Cloud

1.  Push the following files to your GitHub repository:
    * `new_app.py`
    * `requirements.txt` (containing `streamlit` and `spacy`)
2.  **Crucially**, Streamlit Cloud cannot run the `spacy download` command. You must add the spaCy model to your `requirements.txt` as a direct link. Your `requirements.txt` should look like this:
    ```txt
    streamlit
    spacy
    [https://github.com/explosion/spacy-models/releases/download/it_core_news_lg-3.7.0/it_core_news_lg-3.7.0-py3-none-any.whl](https://github.com/explosion/spacy-models/releases/download/it_core_news_lg-3.7.0/it_core_news_lg-3.7.0-py3-none-any.whl)
    ```
    *(Note: You may need to find the latest URL for the `it_core_news_lg` model on the [spaCy models releases page](https://github.com/explosion/spacy-models/releases/)).*
3.  Deploy the app from your GitHub repository on [Streamlit Community Cloud](https://share.streamlit.io/).

## Contributing

Contributions, issues, and feature requests are welcome. Please feel free to open an issue to discuss your ideas.
