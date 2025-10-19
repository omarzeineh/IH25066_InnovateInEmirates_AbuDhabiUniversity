# Fake News Detector Chrome Extension

## 1. Overview

The Fake News Detector is a browser extension that uses an agentic, multi-step AI pipeline to analyze news-like content directly on a webpage. It classifies content, searches for corroborating evidence, and provides a visual indicator of its confidence in the story's authenticity.

## 2. Features

-   **On-Page Analysis:** Automatically scans web pages for articles and posts that look like news.
-   **Visual Indicators:** Injects a colored dot (Green, Orange, Red) next to each detected news block.
-   **Hover for Details:** Users can hover over the dot to see a summary and the top 3 corroborating sources.
-   **Agentic AI Pipeline:** Uses Google's Gemini API for a multi-step analysis process, including classification, search query generation, and fact-checking.
-   **Dynamic Content Support:** Uses a `MutationObserver` to detect and analyze content that is loaded dynamically (e.g., on infinite-scroll social media feeds).

## 3. How It Works

The extension operates in a series of steps orchestrated by the `background.js` service worker:

1.  **Content Identification:** The `content.js` script runs on the active tab, identifying potential news blocks using DOM selectors (e.g., `<article>`, `.post`).
2.  **Classification:** The text content of each block is sent to the Gemini API to be classified as `NEWS` or `NOT NEWS`.
3.  **Search Query Generation:** If a block is classified as `NEWS`, its headline and summary are used to generate relevant Google search queries.
4.  **Fact-Checking via Search:** The extension then uses the Google Custom Search API to find articles from other sources using the generated queries.
5.  **Relativity Analysis:** The search results are passed back to the Gemini API, which acts as a "relativity decider." It compares the original story to the search results to determine a confidence level (`Green`, `Orange`, or `Red`) and extracts the most credible sources.
6.  **UI Update:** The result is sent back to the `content.js` script, which updates the color of the dot and populates the hover-over popup with the analysis details.

## 4. Installation

To install and test the extension locally:

1.  Clone or download this repository.
2.  Open Google Chrome and navigate to `chrome://extensions/`.
3.  Enable **"Developer mode"** using the toggle switch in the top-right corner.
4.  Click on the **"Load unpacked"** button.
5.  Select the `fake-news-detector-extension` folder from the project directory.

The extension's icon should now appear in your Chrome toolbar.

## 5. Configuration

Before the extension can work, you must provide your own API keys for the Google Gemini API and the Google Custom Search API.

1.  Open the file `fake-news-detector-extension/background.js`.
2.  At the top of the file, replace the placeholder values for `GEMINI_API_KEY` and `GOOGLE_SEARCH_API_KEY` with your actual keys:

    ```javascript
    const GEMINI_API_KEY = 'YOUR_GEMINI_API_KEY_HERE';
    const GOOGLE_SEARCH_API_KEY = 'YOUR_GOOGLE_SEARCH_API_KEY_HERE';
    ```

3.  You will also need to create a **Programmable Search Engine** in the Google Cloud Console and get its ID. Replace the placeholder `cx` value in the `callGoogleSearch` function's fetch URL with your Search Engine ID.

4.  After saving the file, go back to `chrome://extensions/` and click the "reload" button for the Fake News Detector extension to apply the changes.

## 6. Usage

Once installed and configured, the extension runs automatically.

-   Navigate to any webpage containing news articles or a social media feed.
-   The extension will automatically identify content and place a gray dot in the top-right corner of each block while it's being analyzed.
-   Once the analysis is complete, the dot will change color:
    -   **Green:** High confidence; corroborated by reputable sources.
    -   **Orange:** Mixed results; some contradictions or less reputable sources.
    -   **Red:** Low confidence; no supporting evidence or analysis failed.
-   Hover over the dot to see a popup with the analyzed headline, a summary, and links to the top sources found.

A `test.html` file is included in the project to allow for easy local testing of the extension's functionality on a variety of news types.

## 7. Project Structure

-   `manifest.json`: The core configuration file for the Chrome extension.
-   `background.js`: The service worker that contains the main agentic logic, API calls, and state management.
-   `content.js`: The script injected into web pages to find content, inject UI elements (dots), and communicate with the background script.
-   `styles.css`: Contains the styling for the injected dots and popups.
-   `popup.html` / `popup.js`: The UI for the extension's icon in the Chrome toolbar.
-   `test.html`: A local test page with various types of news articles to test the extension.
-   `lib/`: Contains the Google Generative AI library.
