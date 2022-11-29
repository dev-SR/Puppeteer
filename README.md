# Puppeteer web scraping and automation

## Setup

### Install dependencies

```bash
yarn add -D nodemon ts-node typescript
yarn add puppeteer
```

script:

```json
{
 "scripts": {
  "dev": "nodemon --exec ts-node index.ts"
 }
}
```

## `evaluate` vs `$$eval`

```ts
import puppeteer from 'puppeteer';
const url = 'https://www.merriam-webster.com/dictionary/ancillary';

async function configureTheBrowser() {
 const browser = await puppeteer.launch({
  headless: true
 });
 const page = await browser.newPage();
 await page.goto(url, { waitUntil: 'load', timeout: 0 });
 return page;
}
async function run() {
 let page = await configureTheBrowser();
 let words = await page.$$eval('.row.entry-header', (elements) =>
  elements.map((e) => ({
   word: e.querySelector('h1.hword')?.textContent,
   syllables: e.querySelector('span.word-syllables-entry')?.textContent
  }))
 );

 // let definitions = await page.$$eval('.vg div.sb-0.sb-entry', (elements) =>
 //  elements.map((e) => ({
 //   definition: e.querySelector('span.dtText')?.textContent,
 //   example: e.querySelector('span.ex-sent.first-child')?.textContent
 //  }))
 // );

 let definitions = await page.evaluate(() => {
  let definitions = [];
  let definitionElements = document.querySelectorAll('.vg div.sb-0.sb-entry');
  definitionElements.forEach((e) => {
   let definition = e.querySelector('span.dtText')?.textContent;
   let example = e.querySelector('span.ex-sent.first-child')?.textContent;
   definitions.push({ definition, example });
  });
  return definitions;
 });

 console.log({
  words,
  definitions
 });
}

run();
```
