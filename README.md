# Para-M-Scope

**Next-Generation Parameter Mapping & Discovery Engine**

Para-M-Scope (Parameter Mapping Scope) is an asynchronous parameter discovery tool that crawls a target website and extracts parameterized URLs. It is fast, lightweight, and designed for recon and web application mapping in InfoSec workflows.

---

## Features

* Asynchronous scanning using `aiohttp` for high-speed requests.
* Smart link extraction (anchors, forms, scripts, images, link tags, and areas).
* Domain-limited crawling option to stay inside the target domain.
* Multi-level logging system with distinct formats for `info`, `debug`, `crawl`, `fetch`, and `params`.
* Clean date–time formatted log output for every log line.
* Suspicious parameter detector for quick XSS/SQL-like pattern hints.
* Rich table output for readable command-line results.
* JSON export with a timestamped report.
* Controls for crawl depth, concurrency, timeout, and maximum pages.

---

## Installation

```bash
# Update package lists and install Python if needed (Termux example)
pkg update -y
pkg install python -y

pip install 
```

---

## Usage

```bash
python .py https://example.com --depth 2 --domain-only --output results.json
```

### Common options

| Flag            | Description                                        |
| --------------- | -------------------------------------------------- |
| `--depth`       | Crawl depth (how many link levels to follow)       |
| `--domain-only` | Restrict crawling to the same domain as the target |
| `--concurrency` | Number of concurrent requests                      |
| `--timeout`     | Per-request timeout in seconds                     |
| `--output`      | Save results to the specified JSON file            |
| `--max-pages`   | Maximum number of pages to crawl                   |
| `--debug`       | Enable debug logging                               |

---

## Output examples

### Logger entry example

```
[2025-01-01]-[19:22:10] [INFO]  | SCOPING depth=1 -> https://example.com
[2025-01-01]-[19:22:11] [PARAMS] | https://example.com/?id=10 -> ['id']
```

### Table result

A rich table displays: index, URL, parameter names, and a quick suspicious indicator.

### JSON output example

```json
{
  "generated_at": "2025-01-01T19:25:32",
  "count": 4,
  "results": [
    {
      "url": "https://example.com/?id=10",
      "params": {"id": ["10"]}
    }
  ]
}
```

---

## How it works

1. Fetch the initial target page asynchronously.
2. Parse the page and extract links from common tags.
3. Normalize links and detect query strings with parameters.
4. Record parameterized URLs and run simple suspicious-value checks.
5. Optionally follow discovered links up to the configured depth.
6. Present results in a rich table and optionally save as JSON.

---

## Notes & Ethics

* This tool is intended for educational and authorized security testing only. Do not use it to scan or attack sites without explicit permission.
* Adjust `--max-pages` and `--concurrency` to avoid overloading targets.

---

## Developer

**Adi (BLACK ZER0)**

Project: Para-M-Scope — Parameter Mapping Scope

---

## Optional additions (available on request)

* PDF/HTML report export
* More advanced XSS/SQL regex detection
* WAF/Cloudflare detection and reporting
* Parameter fuzzing and payload testing
* Additional User-Agent strings and fingerprinting

---

If you want, I can add contribution guidelines, badges, screenshots, or a LICENSE file. Let me know which extras you want and I’ll update the README.
