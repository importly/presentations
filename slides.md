---
theme: the-unnamed
title: Intro to APIs
info: |
  What APIs are, how HTTP works, and how to call APIs from Python,
  ending with a call to a free AI model.
colorSchema: dark
fonts:
  sans: Geist
  mono: Fira Code
drawings:
  persist: false
transition: fade
mdc: true
layout: cover
---

<div class="grid grid-cols-[1fr_1.2fr] gap-10 items-center">
<div>

# Intro to APIs

<p class="sub">How programs talk to each other over the internet, and how to do it from Python.</p>

</div>
<div>

```python
import requests

url = "https://pokeapi.co/api/v2/pokemon/pikachu"
r = requests.get(url)

r.status_code      # 200
r.json()["name"]   # 'pikachu'
```

</div>
</div>

---
layout: section
---

# What is an API?

<p class="sub">The idea, where you've already used them, and the different kinds.</p>

---

# Application Programming Interface

<p class="sub">A way for one program to ask another program for something.</p>

<div class="talk">
  <div class="end">
    <svg viewBox="0 0 64 64" fill="none" stroke="currentColor" stroke-width="1.6" stroke-linecap="round" stroke-linejoin="round">
      <rect x="12" y="14" width="40" height="27" rx="2"/>
      <path d="M6 48h52l-4-7H10z"/>
    </svg>
    <div>Your program</div>
  </div>

  <div class="lanes">
    <div class="lane fwd"><span class="packet req"></span><div class="lbl above">request</div></div>
    <div class="api-tag">API</div>
    <div class="lane back"><span class="packet res"></span><div class="lbl below">response</div></div>
  </div>

  <div class="end">
    <svg viewBox="0 0 64 64" fill="none" stroke="currentColor" stroke-width="1.6" stroke-linecap="round" stroke-linejoin="round">
      <rect x="14" y="8" width="36" height="14" rx="2"/>
      <rect x="14" y="25" width="36" height="14" rx="2"/>
      <rect x="14" y="42" width="36" height="14" rx="2"/>
      <path d="M20 15h.01M20 32h.01M20 49h.01"/>
      <path d="M30 15h14M30 32h14M30 49h14"/>
    </svg>
    <div>Someone else's server</div>
  </div>
</div>

---

# You already use APIs

<div class="rows compact mt-2">
<div class="row"><div class="t">Weather apps</div><div class="d">They don't run satellites. They request data from a weather service.</div></div>
<div class="row"><div class="t">Maps in delivery and ride apps</div><div class="d">Google Maps API</div></div>
<div class="row"><div class="t">Sign in with Google</div><div class="d">An API call to Google's login service</div></div>
<div class="row"><div class="t">Online payments</div><div class="d">Stripe, Razorpay, and similar</div></div>
<div class="row"><div class="t">AI features</div><div class="d">Usually a request to OpenAI, Google, or Anthropic</div></div>
</div>

<p class="mt-6">A lot of modern software is <mark>existing services connected together</mark>.</p>

---

# Types of APIs

<table class="plain mt-4">
<tr><td>Web API (REST)</td><td>Requests over HTTP, data comes back as JSON</td><td>PokeAPI, Gemini API</td></tr>
<tr><td>Library API</td><td>Functions a package gives you</td><td><code>pandas.read_csv()</code></td></tr>
<tr><td>Browser / OS API</td><td>Access to the platform</td><td>Camera, location, notifications</td></tr>
<tr><td>GraphQL</td><td>One endpoint, you ask for exactly the fields you want</td><td>GitHub API v4</td></tr>
<tr><td>WebSockets</td><td>A connection that stays open in both directions</td><td>Chat, multiplayer games</td></tr>
</table>

<p class="muted small !mt-6">When people say "API" without context they usually mean a web API. That's what we're covering today.</p>

---
layout: section
---

# How HTTP works

<p class="sub">URLs, methods, status codes, JSON, headers, and keys.</p>

---

# Request and response

<div class="flow">
  <div class="node">Your code<span>the client</span></div>
  <div class="wires">
    <div class="wire fwd"><div>GET https://pokeapi.co/api/v2/pokemon/pikachu</div></div>
    <div class="wire back"><div>200 OK + {"name": "pikachu", "height": 4, ...}</div></div>
  </div>
  <div class="node">API server<span>pokeapi.co</span></div>
</div>

<div class="kv mt-6" style="--k: 8rem">
<div><b>Request</b></div><div class="d">A method, a URL, headers, and sometimes a body</div>
<div><b>Response</b></div><div class="d">A status code, headers, and a body, usually JSON</div>
</div>

<p class="muted small mt-6">Every API call follows this pattern. The URL and the data change, the structure doesn't.</p>

---

# Parts of a URL

<div class="text-xl mono mt-10 mb-12 whitespace-nowrap">
<span v-mark.underline="{ at: 1, color: '#f87171' }">https://</span><span v-mark.underline="{ at: 2, color: '#fbbf24' }">api.open-meteo.com</span><span v-mark.underline="{ at: 3, color: '#4ade80' }">/v1/forecast</span><span v-mark.underline="{ at: 4, color: '#5eadf2' }">?latitude=28.6&amp;longitude=77.2</span>
</div>

<div class="kv" style="--k: 12rem">
<div v-click="1" class="red">Protocol</div><div v-click="1" class="d">HTTPS is HTTP with encryption</div>
<div v-click="2" class="amber">Host</div><div v-click="2" class="d">Which server you're talking to</div>
<div v-click="3" class="green">Path (endpoint)</div><div v-click="3" class="d">Which resource you want</div>
<div v-click="4" class="blue">Query parameters</div><div v-click="4" class="d"><code>key=value</code> pairs for options and filters, joined with <code>&amp;</code></div>
</div>

---

# HTTP methods

<div class="kv mt-2" style="--k: 10rem">
<div class="mono green">GET</div><div><b>Read</b> data. <span class="muted">Get a Pokemon, get the weather, load a page.</span></div>
<div class="mono amber">POST</div><div><b>Send</b> data or create something. <span class="muted">Submit a form, send a prompt to an AI.</span></div>
<div class="mono blue">PUT / PATCH</div><div><b>Update</b> something that exists.</div>
<div class="mono red">DELETE</div><div><b>Remove</b> something. <span class="muted">Delete a post, cancel an order.</span></div>
</div>

<p class="mt-8">Most of today is <code>GET</code>. The AI call at the end is a <code>POST</code>, because <mark>we send it a prompt</mark>.</p>

---

# Status codes

<div class="rows mt-2">
<div class="row"><div class="t"><span class="green">2xx</span>&nbsp; Success</div><div class="m">200, 201</div><div class="d">It worked. 201 means something was created.</div></div>
<div class="row"><div class="t"><span class="blue">3xx</span>&nbsp; Redirect</div><div class="m">301, 304</div><div class="d">Look somewhere else. <code>requests</code> follows these for you.</div></div>
<div class="row"><div class="t"><span class="amber">4xx</span>&nbsp; Your mistake</div><div class="m">400, 401, 403, 404, 429</div><div class="d">Bad request, bad or missing key, not found, too many requests</div></div>
<div class="row"><div class="t"><span class="red">5xx</span>&nbsp; Their mistake</div><div class="m">500, 503</div><div class="d">The server broke. Not your fault, try again later.</div></div>
</div>

<p class="mt-6">Check the status code before you trust the data. A 404 usually means <mark>a typo in the URL</mark>.</p>

---

# JSON

<p class="sub">The format most web APIs use to send data back.</p>

<div class="grid grid-cols-2 gap-8 mt-6">
<div>

<p class="muted small mb-2">What the API sends</p>

```json
{
  "name": "pikachu",
  "height": 4,
  "types": [
    { "type": { "name": "electric" } }
  ],
  "is_legendary": false
}
```

</div>
<div>

<p class="muted small mb-2">What you get in Python</p>

```python
data = response.json()

data["name"]     # 'pikachu'
data["height"]   # 4
data["types"][0]["type"]["name"]
                 # 'electric'
```

</div>
</div>

<p class="muted small">Objects become dicts, arrays become lists, <code>true</code> / <code>false</code> / <code>null</code> become <code>True</code> / <code>False</code> / <code>None</code>.</p>

---

# Headers

<p class="sub">Extra information sent along with the request and the response.</p>

```http
POST /v1beta/models/gemini-2.5-flash:generateContent HTTP/1.1
Host: generativelanguage.googleapis.com
Content-Type: application/json
x-goog-api-key: AIzaSy...

{ "contents": [ { "parts": [ { "text": "Tell me a joke" } ] } ] }
```

<div class="kv" style="--k: 12rem">
<div>Request headers</div><div class="d">Who you are and what format you're sending</div>
<div>Response headers</div><div class="d">Content type, rate limit info, caching</div>
<div>Body</div><div class="d">The actual data. Used with POST and PUT.</div>
</div>

---

# API keys

<div class="grid grid-cols-2 gap-12 mt-2">
<div>

<p class="muted small">Why APIs ask for one</p>

- To charge you, or enforce a free tier
- To rate limit you
- To keep your data private

<p class="muted small mt-6">Many APIs are open and need no key. We'll start with those.</p>

</div>
<div>

<div class="rows">
<div class="row"><div class="t green">Do</div><div class="d">Keep keys in Colab Secrets or environment variables. If one leaks, delete it and make a new one.</div></div>
<div class="row"><div class="t red">Don't</div><div class="d">Paste keys into code you share or push to GitHub. Bots scan public repos for leaked keys within minutes.</div></div>
</div>

</div>
</div>

---

# Good habits

<div class="grid grid-cols-2 gap-x-12">
<div class="rows">
<div class="row"><div class="t">Read the docs</div><div class="d">Every endpoint, parameter, and limit is listed there</div></div>
<div class="row"><div class="t">Respect rate limits</div><div class="d">Don't call an API in a tight loop</div></div>
<div class="row"><div class="t">Set a timeout</div><div class="d">Never wait forever on a slow server</div></div>
</div>
<div class="rows">
<div class="row"><div class="t">Check the status</div><div class="d">Make sure the request worked before using the data</div></div>
<div class="row"><div class="t">Cache results</div><div class="d">Don't re-fetch data that doesn't change</div></div>
<div class="row"><div class="t">Identify yourself</div><div class="d">Some APIs ask for a <code>User-Agent</code> naming your app</div></div>
</div>
</div>

---
layout: section
---

# Calling APIs from Python

<p class="sub">Real requests to free APIs, in Google Colab.</p>

---

# Open the notebook

<!-- Replace the link below with your Colab share link (Share > Anyone with the link > Viewer) -->
<div class="text-3xl font-semibold tracking-tight mt-6 mb-10 amber">your-colab-link-here</div>

<div class="kv" style="--k: 3rem">
<div class="muted">1</div><div><b>Open the link</b> <span class="muted">and sign in with a Google account</span></div>
<div class="muted">2</div><div><b>File, Save a copy in Drive</b> <span class="muted">so you can edit and run it</span></div>
<div class="muted">3</div><div><b>Run a cell</b> <span class="muted">by clicking it and pressing Shift + Enter. If Colab warns the notebook isn't from Google, click Run anyway.</span></div>
</div>

<p class="mt-8">Nothing to install. Colab already has <code>requests</code>, <mark>the standard Python library for HTTP</mark>.</p>

---

# First request

```python {1|3|4|5|all}
import requests

response = requests.get("https://official-joke-api.appspot.com/random_joke")
joke = response.json()
print(joke["setup"], "...", joke["punchline"])
```

<div class="kv" style="--k: 12rem">
<div class="mono">requests.get(url)</div><div class="d">Sends a GET request and returns a Response object</div>
<div class="mono">.json()</div><div class="d">Parses the response body into a Python dict</div>
<div class="mono">joke["setup"]</div><div class="d">From here on it's regular Python</div>
<div class="muted">Also useful</div><div class="d"><code>.status_code</code>, <code>.text</code>, <code>.headers</code></div>
</div>

---

# Query parameters

<div class="grid grid-cols-[1.8fr_1fr] gap-8 mt-2">
<div>

```python {all|3-7|8-9|11-12}
import requests

params = {
    "latitude": 28.61,
    "longitude": 77.21,
    "current": "temperature_2m,wind_speed_10m",
}
url = "https://api.open-meteo.com/v1/forecast"
r = requests.get(url, params=params, timeout=10)

data = r.json()
print(data["current"]["temperature_2m"], "°C")
```

</div>
<div>

Put options in a dict and pass it as `params`. `requests` builds the URL and escapes special characters.

<div class="aside mt-6 mono break-all">.../forecast?latitude=28.61&amp;longitude=77.21&amp;current=temperature_2m,wind_speed_10m</div>

</div>
</div>

---

# POST requests

<div class="grid grid-cols-[1.8fr_1fr] gap-8 mt-2">
<div>

```python {all|3-7|9-10|11-12}
import requests

new_post = {
    "title": "Hello",
    "body": "My first POST request",
    "userId": 1,
}

url = "https://jsonplaceholder.typicode.com/posts"
r = requests.post(url, json=new_post, timeout=10)
print(r.status_code)   # 201
print(r.json())        # your post, plus an id
```

</div>
<div>

`json=` turns the dict into JSON and sets the `Content-Type: application/json` header for you.

<div class="aside mt-6">JSONPlaceholder is a fake API for practice. Nothing actually gets saved.</div>

</div>
</div>

---

# Handling errors

<div class="grid grid-cols-[1.8fr_1fr] gap-8 mt-2">
<div>

```python {all|3-5|7-10}
import requests

# note the typo in the name
url = "https://pokeapi.co/api/v2/pokemon/pikachuu"
r = requests.get(url, timeout=10)

if r.status_code == 200:
    print(r.json()["name"])
else:
    print("Request failed:", r.status_code)   # 404
```

```python
r.raise_for_status()
# HTTPError: 404 Client Error: Not Found
```

</div>
<div>

Check the status yourself, or call `raise_for_status()` to raise an exception on any 4xx or 5xx.

<div class="aside mt-6">Check before calling <code>.json()</code>. Error responses often aren't JSON.</div>

</div>
</div>

---

# Free APIs to try

<div class="rows compact mt-2">
<div class="row"><div class="t">Official Joke API</div><div class="m mono">official-joke-api.appspot.com/random_joke</div><div class="d">A random joke</div></div>
<div class="row"><div class="t">Dog CEO</div><div class="m mono">dog.ceo/api/breeds/image/random</div><div class="d">A random dog photo</div></div>
<div class="row"><div class="t">PokeAPI</div><div class="m mono">pokeapi.co/api/v2/pokemon/{name}</div><div class="d">Everything about a Pokemon</div></div>
<div class="row"><div class="t">Open-Meteo</div><div class="m mono">api.open-meteo.com/v1/forecast</div><div class="d">Weather for any location</div></div>
<div class="row"><div class="t">Cat Facts / Useless Facts</div><div class="m mono">catfact.ninja/fact</div><div class="d">Random facts</div></div>
</div>

<p class="muted small mt-4">No key needed, open any of these in a browser. Hundreds more at github.com/public-apis/public-apis</p>

---

# Working with a new API

<div class="kv mt-2" style="--k: 3rem">
<div class="muted">1</div><div>Find the docs, usually a <b>Quickstart</b> page</div>
<div class="muted">2</div><div>Check if it needs a key, and how to send it</div>
<div class="muted">3</div><div>Find the endpoint that does what you need</div>
<div class="muted">4</div><div>Try simple GET requests in the browser first</div>
<div class="muted">5</div><div>In Python, print <code>r.json()</code> and <mark>look at its shape</mark></div>
<div class="muted">6</div><div>Pull out the fields you need</div>
<div class="muted">7</div><div>Add a timeout and error handling</div>
</div>

---
layout: section
---

# Calling an AI model

<p class="sub">An LLM is an API like any other. Same request, different server.</p>

---

# An LLM is also an API

<div class="flow">
  <div class="node">Your code<span>the client</span></div>
  <div class="wires">
    <div class="wire fwd"><div>POST .../models/gemini-2.5-flash:generateContent</div></div>
    <div class="wire back"><div>200 OK + {"candidates": [...]}</div></div>
  </div>
  <div class="node">Gemini API<span>Google</span></div>
</div>

<div class="kv mt-6" style="--k: 10rem">
<div class="amber mono">POST</div><div class="d">We're sending a prompt, so it's a POST with a JSON body</div>
<div>API key</div><div class="d">That's how Google applies the free tier limits</div>
<div>Nested JSON</div><div class="d">The answer is a few levels down in the response</div>
</div>

---

# Getting a Gemini API key

<div class="grid grid-cols-2 gap-12 mt-2">
<div>

<div class="kv" style="--k: 2.5rem">
<div class="muted">1</div><div>Go to <b>aistudio.google.com</b> and sign in</div>
<div class="muted">2</div><div><b>Get API key</b>, then <b>Create API key</b></div>
<div class="muted">3</div><div>In Colab, open the <b>Secrets</b> panel (key icon, left sidebar)</div>
<div class="muted">4</div><div>Add <code>GEMINI_API_KEY</code>, turn on notebook access</div>
</div>

</div>
<div>

<p class="muted small mb-2">Then in the notebook</p>

```python
from google.colab import userdata

API_KEY = userdata.get("GEMINI_API_KEY")
```

<div class="aside">The free tier allows a limited number of requests per minute, which is enough for this. Model names change over time, see ai.google.dev/gemini-api/docs/models</div>

</div>
</div>

---

# Calling Gemini with requests

```python {all|1-2|4-5|6|7-8}
MODEL = "gemini-2.5-flash"
url = f"https://generativelanguage.googleapis.com/v1beta/models/{MODEL}:generateContent"

headers = {"x-goog-api-key": API_KEY}
body = {"contents": [{"parts": [{"text": "Explain APIs in two sentences"}]}]}
r = requests.post(url, headers=headers, json=body, timeout=60)
answer = r.json()["candidates"][0]["content"]["parts"][0]["text"]
print(answer)
```

<p>Nothing new: a URL, a header with a key, a JSON body, and <mark>reading nested JSON</mark>.</p>

<p class="muted small">In real projects you'd usually use the official SDK, <code>pip install google-genai</code>. It makes this same HTTP request for you.</p>

---

# Combining APIs

<div class="flow !my-6">
  <div class="node">PokeAPI<span>real data</span></div>
  <div class="wires"><div class="wire fwd"><div>name, types</div></div></div>
  <div class="node">Gemini<span>writes the text</span></div>
</div>

```python
poke = requests.get("https://pokeapi.co/api/v2/pokemon/gengar", timeout=10).json()
types = [t["type"]["name"] for t in poke["types"]]

prompt = f"Write a short poem about {poke['name']}, a {'/'.join(types)} type Pokemon."
print(ask_ai(prompt))
```

<p class="muted small">Many AI apps work this way: fetch real data, put it in the prompt, show the result.</p>

---

# Writing prompts

<div class="grid grid-cols-[1.3fr_1fr] gap-12 mt-2">
<div class="kv" style="--k: 10.5rem">
<div><b>Give a role</b></div><div class="d">"You are a physics teacher..."</div>
<div><b>Say the format</b></div><div class="d">"Answer in 3 bullet points"</div>
<div><b>Include data</b></div><div class="d">Paste in what you fetched from other APIs</div>
<div><b>Ask for JSON</b></div><div class="d">If your code needs to parse the answer</div>
<div><b>Iterate</b></div><div class="d">Change the prompt and compare</div>
</div>
<div class="aside self-start">Groq, OpenRouter, Mistral, and Hugging Face also have free tiers, and work almost the same way: a URL, a key, and a JSON body.</div>
</div>

---

# Exercises

<div class="rows mt-2">
<div class="row"><div class="t">Weather haiku</div><div class="m">Open-Meteo + Gemini</div><div class="d">Get the current weather for your city and have the model write a haiku about it</div></div>
<div class="row"><div class="t">Pokemon battle</div><div class="m">PokeAPI + Gemini</div><div class="d">Fetch two Pokemon and have the model describe who would win and why</div></div>
<div class="row"><div class="t">Fact explainer</div><div class="m">Useless Facts + Gemini</div><div class="d">Get a random fact and ask the model to explain it</div></div>
<div class="row"><div class="t">Dog gallery</div><div class="m">Dog CEO</div><div class="d">Show 5 random photos of one breed, <code>dog.ceo/api/breed/{breed}/images/random</code></div></div>
</div>

---

# Recap

<div class="kv mt-2" style="--k: 7rem">
<div><b>API</b></div><div class="d">A defined way for your code to request something from another service</div>
<div><b>HTTP</b></div><div class="d">Request: method, URL, headers, body. Response: status code, headers, JSON.</div>
<div><b>Python</b></div><div class="d"><code>requests.get()</code> or <code>requests.post()</code>, then <code>.json()</code>, then read the dict</div>
<div><b>Keys</b></div><div class="d">Keep them in secrets, never in code you share</div>
</div>

<p class="mt-8">AI models are called the same way as any other API. <mark>If you can call one, you can call all of them.</mark></p>

---
layout: center
---

# Thanks

<p class="sub text-center">Questions?</p>

<div class="kv mt-10 small" style="--k: 12rem; min-width: 34rem">
<div class="muted">requests docs</div><div>requests.readthedocs.io</div>
<div class="muted">Free API list</div><div>github.com/public-apis/public-apis</div>
<div class="muted">Gemini API docs</div><div>ai.google.dev</div>
<div class="muted">Status codes, with cats</div><div>http.cat</div>
</div>
