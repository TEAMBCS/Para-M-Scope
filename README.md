<p align="center">
  <img src="https://i.postimg.cc/0yp6ZH3K/Para-M-Scope.png" width="380" alt="Para-M-Scope Logo">
</p>

<p align="center">
  ⚙️ Built with ❤️ by <b>BLACK ZERO</b><br>  
  🚀 Powered by <b>BANGLADESH CYBER SQUAD</b><br>
  📆 Year: 2025
</p>

---

# Para-M-Scope

**Next-Generation Parameter Mapping & Discovery Engine**

Para-M-Scope (Parameter Mapping Scope) is an asynchronous parameter discovery tool that crawls a target website and extracts parameterized URLs. It is fast, lightweight, and designed for recon and web application mapping in InfoSec workflows.

---

## Install requirements and run:

```bash
pkg update -y
pkg install libxml2 -y
pkg install libxslt -y
pkg install python -y
pkg install python3 -y
pkg install git -y
git clone https://github.com/TEAMBCS/Para-M-Scope.git
cd Para-M-Scope
chmod +x *
chmod +w .
chmod 777 *
pip3 install -r para-m-scope.txt
python3 para-m-scope.py --help
```
### Another Way to Install 

```bash
pip install para-m-scope==1.2.1
```


---

## Features

* Async crawling using `aiohttp` for high-speed requests.
* Smart link extraction from anchors, forms, scripts, images, link tags and areas.
* Domain-limited crawling option to stay inside the target domain.
* Multi-level logging (info, debug, crawl, fetch, params) with a clean datetime format.
* Suspicious parameter detector for quick XSS / SQL-like pattern hints.
* **New**: Parameter analysis features:

  * Parameter fuzzing (small payloads) to detect errors / anomalies.
  * Reflected parameter detection (potential reflected XSS).
  * Common vulnerability keyword detection (SQL/parsing error keywords).
* Rich table output and optional JSON export.
* Controls for depth, concurrency, timeout, and max pages.

---

## New: Fuzzing & Analysis — what it does

Para-M-Scope now includes lightweight analysis for each discovered parameterized URL:

* **Parameter fuzzing**
  Appends small payloads (`'`, `"`, `<`, `>`, `--`) to parameter values and requests the modified URL. Checks for:

  * Error keywords in the response body (common SQL/parse errors).
  * Significant response length changes (heuristic for unexpected output).
  * Redirects / changed final URL.

* **Reflected detection**
  Checks whether a parameter value is present in the response body (possible reflected XSS indicator).

* **Common vulnerability keywords check**
  Scans responses for terms like `mysql`, `syntax error`, `sqlstate`, `unexpected token`, `pdoexception`, etc. If found, the tool logs a `SUSPECT` note.

Logs produced include:

* `PARA-REFLECT` — parameter value reflected in response.
* `FUZZ-ERR` — fuzzed request produced error-keywords in response.
* `FUZZ-LEN` — fuzzed request produced significant length change (>30% by default).
* `SUSPECT` — base response already contains error keywords.

> Analysis runs asynchronously and non-blocking, so the crawler proceeds while analysis tasks run in background.

---

## Usage & Options

Show help:

```bash
python3 para-m-scope.py --help
```

Example:

```bash
python3 para-m-scope.py https://example.com --depth 2 --domain-only --concurrency 10 --timeout 20 --max-pages 100 --output results.json
```

**Common options**

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

## Output

### Logger example

```
[2025-01-01]-[19:22:10] [INFO]  | [SCOPING] depth=1 -> https://example.com
[2025-01-01]-[19:22:11] [INFO]  | [PARA-MS] https://example.com/?id=10 -> ['id']
[2025-01-01]-[19:22:12] [INFO]  | [PARA-REFLECT] ➤ [params] https://example.com/?id=10 :: param 'id' appears reflected in response
[2025-01-01]-[19:22:14] [INFO]  | [FUZZ-ERR] ➤ [fetch] https://example.com/?id=10' -> keywords ['mysql']
```

### Table result

CLI table shows: index, URL, parameter names, and a quick suspicious indicator (reflection/fuzz hints included).

### JSON output example (schema)

Saved JSON contains `generated_at`, `count`, and `results`. Each result now optionally includes `analysis`:

```json
{
  "generated_at": "2025-01-01T19:25:32",
  "count": 1,
  "results": [
    {
      "url": "https://example.com/?id=10",
      "params": {"id": ["10"]},
      "analysis": {
        "reflections": {"id": true},
        "fuzz": {
          "id": [
            {"payload": "'", "final_url": "https://...", "errors": ["mysql"], "len_change_pct": 4.5},
            {"payload": "\"", "final_url": "https://...", "errors": [], "len_change_pct": 0.0}
          ]
        },
        "error_keywords_in_base": []
      }
    }
  ]
}
```

---

## Examples

Basic scan:

```bash
python3 para-m-scope.py https://target.example --depth 2 --concurrency 8
```

Domain-only scan:

```bash
python3 para-m-scope.py https://target.example --depth 3 --domain-only --max-pages 100
```

Save results:

```bash
python3 para-m-scope.py https://target.example --output /path/to/results.json
```

---

## Troubleshooting & Tips

* `bs4.FeatureNotFound: lxml`
  Install system libs and lxml:

  ```bash
  pkg install libxml2 libxslt -y
  pip3 install lxml
  ```

* `Session is closed` or related aiohttp errors
  Make sure the script is run normally (`asyncio.run(main())`) and dependencies are current. Use `--debug` to get more logs.

* `Too many open files` (Termux)
  Increase FD limit and reduce concurrency:

  ```bash
  python3 para-m-scope.py ... --concurrency 6
  ```

* Slow network / timeouts
  Increase `--timeout` (e.g., 20 or 30).

---


## Performance tips (Termux)

* Increase file descriptor limit: `ulimit -n 4096`
* Lower concurrency for unstable networks: `--concurrency 4-8`
* Use `--max-pages` to limit scope on large sites
* Run scans from a stable network or via a controlled proxy

---

## Responsible use & legal

**Important:** Only scan systems you own or have explicit permission to test. Misuse can be illegal.

If you discover a vulnerability:

* Do not exploit it.
* Follow responsible disclosure: contact the site owner/security contact.
* Provide minimal PoC data required to verify.


---

## How to interpret findings (quick guide)

| Indicator      | Meaning / suggested action                                                        |
| -------------- | --------------------------------------------------------------------------------- |
| `PARA-REFLECT` | Parameter value appears in response — check manually for XSS                      |
| `FUZZ-ERR`     | Error keywords found after fuzz — possible SQLi or parser error (verify manually) |
| `FUZZ-LEN`     | Large length change on fuzz — inspect response for injection output               |
| `SUSPECT`      | Base response contained error keywords — investigate further                      |

> All findings are heuristics. Manual verification is required before any claim.

---

## Contributing

Contributions welcome. Suggested workflow:

* Fork → create branch → PR
* Follow PEP8 and keep functions small and testable
* Add tests for new analysis logic
* Update `CHANGELOG.md` for any behavior changes

---

## Roadmap

Planned / optional improvements:

* HTML/interactive report (Streamlit or static HTML)
* Plugin architecture for additional analyzers
* Rate-limiter + politeness settings (random delays)
* Web dashboard + API export (webhook/Discord/Telegram)

---

## Changelog (short)

* v1.0 — Initial release (core crawling + param mapping)
* v1.1 — Added async parameter analysis: fuzzing, reflection, error keyword detection

---
## 🖼️ MENU PIC
<p align="center">
  <img src="https://i.postimg.cc/kGJFJkH2/TOOL-MENU.jpg" alt="pic" width="50%" />
</p>

---

## 🖼️ DEMO PIC
<p align="center">
  <img src="https://i.postimg.cc/j2Lkntsm/demo1.jpg" alt="pic1" width="45%" />
  &nbsp;&nbsp;
  <img src="https://i.postimg.cc/SNYvyS1M/demo2.jpg" alt="pic2" width="45%" />
  &nbsp;&nbsp;
  <img src="https://i.postimg.cc/FKNWgjrY/demo3.jpg" alt="pic3" width="45%" />
 
  </p>

---

## License

*MIT License © 2025 Bangladesh Cyber Squad*

---
