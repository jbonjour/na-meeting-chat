# NA Meeting Finder

A chat-based web app for finding Narcotics Anonymous meetings. Users type natural language questions like "Friday evening meetings in Portland" and receive formatted meeting results from the BMLT (Basic Meeting List Toolbox) aggregator.

Built as a single static `index.html` file — no build step, no server required, no API keys needed.

---

## Features

- Smart keyword parser understands natural language — no AI API required
- Understands days, times, venues, formats, cities, and "near me" searches
- Live data from the BMLT aggregator API
- Mobile-friendly, responsive design with NA branding
- Supports geolocation ("meetings near me")
- Handles in-person, virtual, and hybrid meetings
- Embeddable in Google Sites via iframe
- Free to run — no ongoing costs

---

## What You Can Ask

- "Friday evening meetings"
- "Virtual meetings after 7pm"
- "Meetings near me"
- "Open meetings on weekends"
- "Step study meetings in Portland"
- "Women's meetings tomorrow"
- "Tonight's meetings within 5 miles"
- "Noon meetings on Monday"

---

## Setup

### 1. Configure the App

```bash
cp config.example.js config.js
```

Open `config.js` and set your area name, BMLT server, and service body ID:

```js
const CONFIG = {
  AREA_NAME: "Portland NA",
  BMLT_ROOT_SERVER: "https://bmlt.wszf.org/main_server",
  DEFAULT_SERVICE_BODY_ID: 39  // Portland NA=39, Contra Costa NA=26; null = search all
};
```

`config.js` is listed in `.gitignore` and will never be committed.

### 2. Run Locally

Just open `index.html` in a browser. No web server needed for basic use.

> **Note:** Geolocation ("near me") requires the page to be served over HTTPS in production. It will work on `localhost` for development.

---

## Deploy to GitHub Pages

### First-time setup

1. Push the repo to GitHub (do **not** push `config.js`):

   ```bash
   git init
   git add index.html config.example.js .gitignore README.md
   git commit -m "Initial commit"
   git remote add origin https://github.com/YOUR_USERNAME/na-meeting-chat.git
   git push -u origin main
   ```

2. In your GitHub repo, go to **Settings → Pages**
3. Under **Source**, choose **Deploy from a branch** → `main` → `/ (root)`
4. Click **Save**
5. Your app will be live at `https://YOUR_USERNAME.github.io/na-meeting-chat/`

### config.js on GitHub Pages

Since `config.js` is gitignored, use a GitHub Actions workflow to write it from a repository variable at deploy time:

```yaml
# .github/workflows/deploy.yml
name: Deploy
on:
  push:
    branches: [main]
jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Write config
        run: |
          cat > config.js << EOF
          const CONFIG = {
            AREA_NAME: "${{ vars.AREA_NAME }}",
            BMLT_ROOT_SERVER: "${{ vars.BMLT_ROOT_SERVER }}",
            DEFAULT_SERVICE_BODY_ID: ${{ vars.DEFAULT_SERVICE_BODY_ID }}
          };
          EOF
      - uses: actions/configure-pages@v5
      - uses: actions/upload-pages-artifact@v3
        with:
          path: .
      - uses: actions/deploy-pages@v4
```

Add `AREA_NAME`, `BMLT_ROOT_SERVER`, and `DEFAULT_SERVICE_BODY_ID` under **Settings → Secrets and variables → Actions → Variables**.

---

## Embed in Google Sites

1. In Google Sites, click **Insert → Embed**
2. Select **By URL** and paste your GitHub Pages URL:
   ```
   https://YOUR_USERNAME.github.io/na-meeting-chat/
   ```
3. Click **Insert**
4. Resize the iframe block to your preferred height (600–800px recommended)

> Google Sites embeds external pages in an iframe. The app is designed to work correctly within iframes — it uses `height: 100dvh` and a fixed input bar so the interface fills the embedded area properly.

---

## Customization

| Setting | Where | Description |
|---|---|---|
| `AREA_NAME` | `config.js` | Displayed in the header and browser tab title |
| `DEFAULT_SERVICE_BODY_ID` | `config.js` | Limits search to a specific NA region. Set to `null` to search all. Find your region's ID at your BMLT root server. |
| `BMLT_ROOT_SERVER` | `config.js` | Point to a different BMLT root server if needed. |
| Header colors | `index.html` CSS | Deep blue `#003366`, gold `#FFB800` — change to match your area's branding. |

---

## Multi-Area Deployment

Each NA area can run its own instance with its own `config.js`:

- Fork this repo for each area
- Set `AREA_NAME`, `BMLT_ROOT_SERVER`, and `DEFAULT_SERVICE_BODY_ID` in that area's config
- Deploy to GitHub Pages under that area's GitHub organization

---

## How It Works

1. User types a natural language query
2. A built-in keyword parser extracts search parameters (days, times, location, venue type, meeting formats, etc.)
3. The structured params are mapped to BMLT API query parameters
4. Results are fetched from the BMLT JSON API and rendered as meeting cards

### Parser capabilities

| Pattern | Example |
|---|---|
| Days of week | "Friday", "weekend", "weekdays" |
| Relative days | "today", "tomorrow", "tonight" |
| Explicit times | "after 7pm", "before 9", "at 7:30" |
| Time of day | "morning", "noon", "afternoon", "evening", "night" |
| Venue type | "virtual", "online", "zoom", "in-person", "hybrid" |
| Geolocation | "near me", "closest", "within 10 miles" |
| City | "in Portland", "in Walnut Creek" |
| Meeting format | "step study", "speaker", "women's", "men's", "young people", "candlelight", "newcomer", etc. |

---

## Privacy

- No data is stored or logged by this app
- Queries are processed entirely in the user's browser
- Geolocation is only requested if the user asks for "meetings near me"
- No third-party analytics or tracking

---

## License

MIT — free to use, modify, and distribute for NA community purposes.
