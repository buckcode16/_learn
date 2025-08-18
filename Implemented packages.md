
---

### 1. Environment & Configuration

- **Loading secrets from `.env`** (`python‑dotenv`)  
    Tags: `get_items_sku_stock_parallel.py`, `get_order_detail_component_parallel.py`, `shopify_webhook.py`, `smaregi_webhook.py`, `product_lookup_cache.py`, `shopify_integration.py`, `smaregi_integration.py`
    
- **Timezone‑aware datetimes** (`datetime.timezone`)  
    Tags: `get_items_sku_stock_parallel.py`, `get_order_detail_component_parallel.py`
    
- **Script entrypoint** (`if __name__ == "__main__":`)  
    Tags: `get_items_sku_stock_parallel.py`, `get_order_detail_component_parallel.py`, `rpp_extract.py`, `rpp_rename.py`, `extract-img-dropbox.py`, `shopify_integration.py`, `smaregi_integration.py`
    

---

### 2. HTTP Requests & API Authentication

- **Synchronous HTTP** (`httpx.Client` / `requests`)  
    Tags: `get_items_sku_stock_parallel.py`, `get_order_detail_component_parallel.py`, `shopify_integration.py`, `smaregi_integration.py`
    
- **Asynchronous HTTP** (`httpx.AsyncClient` + `asyncio`)  
    Tags: `get_items_sku_stock_parallel.py`, `get_order_detail_component_parallel.py`
    
- **MD5 signature generation** (`hashlib.md5`)  
    Tags: `get_items_sku_stock_parallel.py`, `get_order_detail_component_parallel.py`
    
- **Retry & exponential backoff**  
    Tags: `get_items_sku_stock_parallel.py`, `get_order_detail_component_parallel.py`
    
- **Pagination patterns**
    
    - Cursor‑based (GraphQL) → `shopify_integration.py`
        
    - Page‑number (REST) → `smaregi_integration.py`
        
    - “Last seen” ID loop → `get_items_sku_stock_parallel.py`, `get_order_detail_component_parallel.py`
        
- **OAuth2 client‑credentials**  
    Tags: `smaregi_integration.py`
    
- **HMAC webhook signature verification**  
    Tags: `shopify_webhook.py`
    

---

### 3. Concurrency & Rate‑Limiting

- **`asyncio` fundamentals** (`async def`, `await`, event loop)  
    Tags: `get_items_sku_stock_parallel.py`, `get_order_detail_component_parallel.py`
    
- **Task orchestration** (`asyncio.gather`, `return_exceptions=True`)  
    Tags: `get_items_sku_stock_parallel.py`, `get_order_detail_component_parallel.py`
    
- **Semaphore‑based rate‑limiting**  
    Tags: `get_items_sku_stock_parallel.py`, `get_order_detail_component_parallel.py`
    
- **Connection pool limits** (`httpx.Limits`)  
    Tags: `get_items_sku_stock_parallel.py`, `get_order_detail_component_parallel.py`
    

---

### 4. Data Parsing & Transformation

- **XML parsing & tree‑building** (`xml.etree.ElementTree`)  
    Tags: `get_items_sku_stock_parallel.py`, `get_order_detail_component_parallel.py`
    
- **CSV reading/writing** (`csv` module)  
    Tags: `rpp_extract.py`, `rpp_rename.py`
    
- **JSON parsing** (`response.json()`, `await request.json()`)  
    Tags: `shopify_integration.py`, `smaregi_integration.py`, `shopify_update.py`, `smaregi_update.py`, `shopify_webhook.py`, `smaregi_webhook.py`
    
- **Data merging/enrichment** (e.g. merging stock lists)  
    Tags: `smaregi_integration.py`, `sync_manager.py`
    

---

### 5. File & Directory Operations

- **Directory traversal & filtering** (`os.listdir`, `os.path.isdir`)  
    Tags: `rpp_extract.py`, `rpp_rename.py`
    
- **Temporary files** (`tempfile.NamedTemporaryFile`)  
    Tags: `get_items_sku_stock_parallel.py`, `get_order_detail_component_parallel.py`
    
- **Encoding fallbacks** (`UnicodeDecodeError`)  
    Tags: `rpp_extract.py`
    
- **Filename sanitization & collision‑avoidance**  
    Tags: `rpp_rename.py`
    
- **Moving/renaming & cleanup** (`shutil.move`, `os.rename`, `os.rmdir`)  
    Tags: `rpp_rename.py`
    
- **Excel via Pandas & Openpyxl** (`pd.ExcelFile`, `load_workbook`, `DataFrame`)  
    Tags: `extract-img-dropbox.py`
    
- **Image extraction/insertion in Excel** (`openpyxl.drawing.image.Image`, anchor math)  
    Tags: `extract-img-dropbox.py`
    
- **In‑memory caching** (dicts for file lists, bytes, parsed data)  
    Tags: `extract-img-dropbox.py`
    
- **Byte streams** (`io.BytesIO`)  
    Tags: `extract-img-dropbox.py`
    

---

### 6. Logging & Error Handling

- **Structured logging & prints** (contextual messages)  
    Tags: _every script_
    
- **Granular exception handling** (multiple `except` clauses)  
    Tags: _every script_
    
- **HTTP error handling & FastAPI exceptions**  
    Tags: `shopify_webhook.py`, `smaregi_webhook.py`, `shopify_update.py`, `smaregi_update.py`
    

---

### 7. Web Framework & Endpoints

- **FastAPI routers** (`APIRouter`, `Body`, `Header`, `HTTPException`)  
    Tags: `shopify_update.py`, `shopify_webhook.py`, `smaregi_update.py`, `smaregi_webhook.py`, `sync_manager.py`, `main.py`
    

---

### 8. Caching & Data Stores

- **Redis for product lookups & reverse mappings** (`redis.from_url`, `hset`, `get`, `scan`)  
    Tags: `product_lookup_cache.py`
    

---

### 9. Integration Orchestration

- **Delta‑based sync pattern** (single source of truth → compute delta → update cache → update target system)  
    Tags: `sync_manager.py`
    

---

### 10. GraphQL Operations

- **Crafting queries & mutations** (GraphQL over HTTP, headers, JSON payloads)  
    Tags: `shopify_integration.py`
    

---

### 11. Regular Expressions

- **Pattern validation** (barcodes, manufacturer codes)  
    Tags: `rpp_extract.py`, `extract-img-dropbox.py`
    

---

### 12. Notebook Data Requests

- **Ad‑hoc HTTP & data exploration** in Jupyter (`requests`, `pandas`)  
    Tags: `request.ipynb`
    

---



## 🟢 Standard Library

- **os**
    
    - `os.getenv()`, `os.environ`
        
    - File ops: `os.remove()`, `os.rmdir()`, `os.listdir()`, `os.rename()`
        
    - Path checks: `os.path.isdir()`, `os.path.exists()`
        
    - Tags: `get_items_sku_stock_parallel.py`, `get_order_detail_component_parallel.py`, `rpp_extract.py`, `rpp_rename.py`, `shopify_webhook.py`, `smaregi_webhook.py`, `smaregi_integration.py`, `shopify_integration.py`, `product_lookup_cache.py`
        
- **tempfile**
    
    - `tempfile.NamedTemporaryFile()`
        
    - Tags: `get_items_sku_stock_parallel.py`, `get_order_detail_component_parallel.py`
        
- **io**
    
    - `io.BytesIO`
        
    - Tags: `extract-img-dropbox.py`
        
- **csv**
    
    - `csv.reader`, `csv.writer`
        
    - Tags: `rpp_extract.py`, `rpp_rename.py`
        
- **shutil**
    
    - `shutil.move()`
        
    - Tags: `rpp_rename.py`
        
- **datetime**
    
    - `datetime.datetime.now()`, `datetime.timedelta`, `datetime.timezone`
        
    - Formatting: `.strftime()`
        
    - Tags: `get_items_sku_stock_parallel.py`, `get_order_detail_component_parallel.py`
        
- **copy**
    
    - `copy.deepcopy()`
        
    - Tags: `get_order_detail_component_parallel.py`
        
- **asyncio**
    
    - `asyncio.run()`, `asyncio.gather()`, `asyncio.Semaphore()`
        
    - Tags: `get_items_sku_stock_parallel.py`, `get_order_detail_component_parallel.py`
        
- **hashlib**
    
    - `hashlib.md5()`
        
    - Tags: `get_items_sku_stock_parallel.py`, `get_order_detail_component_parallel.py`, `shopify_webhook.py`
        
- **hmac**
    
    - `hmac.new()`, `hmac.compare_digest()`
        
    - Tags: `shopify_webhook.py`
        
- **base64**
    
    - `base64.b64decode()`
        
    - Tags: `shopify_webhook.py`
        
- **json**
    
    - `json.loads()`, `json.dumps()`
        
    - Tags: `product_lookup_cache.py`, `shopify_webhook.py`, `smaregi_webhook.py`
        
- **logging**
    
    - `logging.getLogger()`, `logging.info()`, `logging.error()`
        
    - Tags: `shopify_integration.py`, `sync_manager.py`
        
- **re**
    
    - `re.compile()`, `re.match()`, regex flags (e.g. `re.IGNORECASE`)
        
    - Tags: `extract-img-dropbox.py`
        
- **traceback**
    
    - `traceback.print_exc()`
        
    - Tags: `get_items_sku_stock_parallel.py`, `get_order_detail_component_parallel.py`
        

---

## 🔵 Third‑Party Libraries

- **python‑dotenv**
    
    - `load_dotenv()` to pull in `.env`
        
    - Tags: everywhere you see `load_dotenv()` (`get_items_…`, `get_order_…`, `shopify_webhook.py`, `smaregi_webhook.py`, `shopify_integration.py`, `smaregi_integration.py`, `product_lookup_cache.py`)
        
- **httpx**
    
    - **Sync**: `httpx.Client()`, `client.get()`, `client.post()`, `response.raise_for_status()`, `response.json()`
        
    - **Async**: `httpx.AsyncClient()`, `Limits(max_connections=…, max_keepalive_connections=…)`, `timeout=`
        
    - Tags: `get_items_sku_stock_parallel.py`, `get_order_detail_component_parallel.py`, `shopify_integration.py`, `smaregi_integration.py`
        
- **redis**
    
    - `redis.from_url()`, `client.hset()`, `client.hgetall()`, `client.scan()`/`scan_iter()`
        
    - Tags: `product_lookup_cache.py`, `sync_manager.py`
        
- **fastapi**
    
    - `FastAPI()`, `APIRouter()`, route decorators (`@router.post`, `@app.get`), dependency parameters (`Body`, `Header`, `Query`, `Request`), `HTTPException`
        
    - Tags: `main.py`, `shopify_update.py`, `smaregi_update.py`, `shopify_webhook.py`, `smaregi_webhook.py`, `sync_manager.py`
        
- **openpyxl**
    
    - `load_workbook()`, `openpyxl.drawing.image.Image`, worksheet `.add_image()`
        
    - Tags: `extract-img-dropbox.py`
        
- **pandas**
    
    - `pd.ExcelFile()`, `.parse()`, `pd.DataFrame()`, `.iterrows()`
        
    - Tags: `extract-img-dropbox.py`, `request.ipynb`
        
- **dropbox** (official SDK)
    
    - `dropbox.Dropbox()`, `files_list_folder()`, `files_list_folder_continue()`, `files_get_metadata()`, `files_download()`
        
    - Tags: `extract-img-dropbox.py`
        
- **dropbox_uploader** (your wrapper)
    
    - `upload_file_to_dropbox()`
        
    - Tags: `get_items_sku_stock_parallel.py`, `get_order_detail_component_parallel.py`
        
- **requests** _(in your Jupyter notebook)_
    
    - `requests.get()`, `response.status_code`, `response.json()`
        
    - Tags: `request.ipynb`
        

---

## 🟣 Internal Modules

- **services**
    
    - Domain‑specific helpers: e.g. `update_shopify_inventory()`, `fetch_smaregi_stock_data()`, `process_inventory_sync()`, `get_product_by_shopify_id()`
        
    - Tags: `shopify_update.py`, `smaregi_update.py`, `shopify_webhook.py`, `smaregi_webhook.py`, `sync_manager.py`, `main.py`
        
- **cache**
    
    - Wrappers around Redis calls for inventory lookup (`get_inventory()`, `set_inventory()`)
        
    - Tags: `sync_manager.py`
        
- **api** / **_backup**
    
    - Local test endpoints
        
    - Tags: `main.py`
        

---

