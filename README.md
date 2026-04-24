[README.md](https://github.com/user-attachments/files/27056753/README.md)
# 🐛 Kan-of-Worms Generator

**Absurd Solutions for Everything**

A satirical cartoon product idea generator powered by Claude AI. Type in any everyday topic — coffee, gym culture, traffic, social media — and it spits out 3 shelf-ready "Kan-of-Worms" product concepts, complete with label copy, mascot illustration notes, and the humor technique used.

Created by **Studio Bits of Gravity** · Happy Grouch Cartoonist · [@ChatterWhackys](https://quirkyflashchronicles.com)

---

## 🏗️ How It Works

1. User types a topic + optional humor filter(s)
2. The page POSTs to a **Cloudflare Worker** proxy
3. The proxy forwards the request to **Anthropic's Claude API**
4. Results are rendered as styled Kan-of-Worms product cards

---

## 🚀 Setup

### 1. Fork / Clone this repo

```bash
git clone https://github.com/YOUR_USERNAME/kan-of-worms-generator.git
```

### 2. Deploy the Cloudflare Worker proxy

You already have a proxy running at `gravificer-proxy.mvphillips.workers.dev`.

If you need a fresh one, use this `worker.js`:

```js
export default {
  async fetch(request, env, ctx) {
    if (request.method === 'OPTIONS') {
      return new Response(null, {
        headers: {
          'Access-Control-Allow-Origin': '*',
          'Access-Control-Allow-Methods': 'POST, OPTIONS',
          'Access-Control-Allow-Headers': 'Content-Type',
        },
      });
    }
    if (request.method !== 'POST') {
      return new Response('Method not allowed', { status: 405 });
    }
    const body = await request.json();
    const maxRetries = 3;
    const delays = [2000, 4000, 8000];
    for (let attempt = 0; attempt <= maxRetries; attempt++) {
      const response = await fetch('https://api.anthropic.com/v1/messages', {
        method: 'POST',
        headers: {
          'Content-Type': 'application/json',
          'x-api-key': env.ANTHROPIC_API_KEY,
          'anthropic-version': '2023-06-01',
        },
        body: JSON.stringify(body),
      });
      const data = await response.json();
      if (data.error?.type === 'overloaded_error' && attempt < maxRetries) {
        await new Promise(r => setTimeout(r, delays[attempt]));
        continue;
      }
      return new Response(JSON.stringify(data), {
        headers: {
          'Content-Type': 'application/json',
          'Access-Control-Allow-Origin': '*',
        },
      });
    }
  }
};
```

Set your `ANTHROPIC_API_KEY` as a Cloudflare Worker **secret**.

### 3. Update the proxy URL in `index.html`

Find this line near the top of the `<script>` block:

```js
const PROXY_URL = 'https://gravificer-proxy.mvphillips.workers.dev/';
```

Replace with your own Cloudflare Worker URL if different.

### 4. Deploy to GitHub Pages

- Go to your repo **Settings → Pages**
- Set source to `main` branch, `/ (root)`
- Your generator will be live at: `https://YOUR_USERNAME.github.io/kan-of-worms-generator/`

---

## 🎭 Humor Filters

The generator knows 11 comedy techniques:

| Filter | Description |
|--------|-------------|
| 🔄 Irony | Intended meaning is the opposite of literal |
| 🎭 Character | Comedic character acting on exaggerated traits |
| 📌 Reference | Relatable real-life reference |
| 😱 Shock | Bathroom humor, gross-out, taboo |
| 📢 Hyperbole | Exaggerated to an impossible extreme |
| 🎬 Parody | Closely mimics something well-known |
| 🔤 Wordplay | Puns, double meanings, rhymes |
| 🔗 Analogy | Comparing two wildly different things |
| 🤪 Madcap | Pure silly nonsense |
| 🪞 Metahumor | Humor about humor |
| 🔍 Misplaced Focus | Deliberately focusing on the wrong detail |

---

## 📝 WordPress Embed

To add this to your Quirky Flash Chronicles post, embed as an iframe:

```html
<iframe 
  src="https://YOUR_USERNAME.github.io/kan-of-worms-generator/" 
  width="100%" 
  height="900" 
  frameborder="0"
  style="border-radius: 12px;"
></iframe>
```

Or just link directly to the GitHub Pages URL.

---

## 🐛 Part of the Kan-of-Worms Universe

- Pessimism Pills — *Eliminate Hyper-Optimism*
- GMR Grumpy Mood Remover — *For All Grumpy People*
- Self-Help Detox — *For Self-Improvement Saturation*

*More kans incoming...*

---

**Happy Grouch Cartoonist · Quirky Flash Chronicles · Studio Bits of Gravity**
