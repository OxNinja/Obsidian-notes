#chrome #debug #async 
https://playwright.dev
Pour interagir avec un [[Chromium]] à distance

```python
import asyncio
from playwright.async_api import async_playwright

async def run(playwright):
    webkit = playwright.webkit
    browser = await webkit.launch()
    context = await browser.new_context()
    page = await context.new_page()
    await page.goto("https://example.com")
    await page.screenshot(path="screenshot.png")
    await browser.close()

async def main():
    async with async_playwright() as playwright:
        await run(playwright)

asyncio.run(main())
```

Existe en Python et NodeJS par exemple
`browser = chromium.connect_over_cdp('http://localhost:9000')`
