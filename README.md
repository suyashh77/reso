# Reso

Reso is a personal canvas for music memories. It's a place where you can save the stories behind the songs that shape you—not as ratings, not as playlists, but as individual memory cards.

This repository contains the static landing page and interactive prototype designed to validate the Reso concept and collect early sign-ups.

## Project Structure

- `index.html`: The main landing page. It includes the core pitch, a gallery of memory cards, feature overviews, and a fully interactive app demo. It also features a built-in early access waitlist form.
- `why.html`: A deep dive into the philosophy behind Reso, the "memory card" format, and the research backing the four pillars of music's impact on people (Identity, Memory, Emotion, Belonging).
- `liner-product-doc.md`: The initial product strategy document containing competitive analysis, target users, and future app architecture plans.
- **Local Assets**: All images (`propsal-card.webp`, `dog_card.webp`, `locked-in-kobe.jpeg`, etc.) used to display the memory cards in the UI.

## Features Included

1. **Interactive Demo**: Built directly into `index.html`. Users can test the entire "Add Memory" flow, pick a mood, compose a journal entry, filter privacy settings, and generate a new memory card in the canvas.
2. **Local Waitlist w/ CSV Export**: The interest list at the bottom tracks user responses (Name, Email, and Feedback) locally during the session. It includes a smart "Admin: Download responses (CSV)" button that instantly packages all captured sign-ups into a clean spreadsheet file for you.

## Deployment (GitHub Pages)

This project is completely static HTML/CSS/JS, mapped perfectly with relative paths. To host it for free online:

1. Upload all files (HTML and all images) into a new GitHub repository. Ensure `index.html` is at the root level of the repo.
2. Head to the repository's **Settings** tab.
3. Click on **Pages** in the left sidebar.
4. Under the "Build and deployment" section, set the deployment source to **Deploy from a branch**.
5. Select the `main` branch (or whichever branch you uploaded the files to) and click **Save**.
6. GitHub will process it and provide you with a live, shareable URL to your site!
