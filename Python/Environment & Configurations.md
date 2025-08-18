
The  [Twelve-Factor App](https://12factor.net/) suggests best practices for keeping secrets out of source control
In [III. Config](https://12factor.net/config), Apps config requires strict separation from code. 

> [!Note]
*A litmus test for whether an app has all config correctly factored out of the code is whether the codebase could be made open source **at any moment**, without compromising any credentials.*

### Library
---
#### python-dotenv

`load_dotenv()` searches `.env` in your current working directory and then each parent directory.
`load_dotenv()` won’t raise if `.env` is absent.

> [!tip] Best practice 
```python
from dotenv import load_dotenv, find_dotenv
import os, sys
if not find_dotenv():
    sys.exit(".env file not found")

load_dotenv()  # now safe to assume variables exist> 
```


#### Regarding the necessity of using `find_dotenv` ....
- **`find_dotenv()`** returns the path it discovered (e.g. `"/home/alice/project/.env"`), or an empty string if it didn’t find one.
- **`load_dotenv()`** under the hood just swallows that and returns a boolean indicating success— but we don’t see _where_ it came from.


Loads `.env` files into `os.environ`
```python
os.getenv("ENV")         # returns None if missing 
os.environ["ENV"]         # KeyError if missing
```
`os.getenv`
- Looks up `key` in the environment.
`os.environ`
- If the key is missing, returns the `default` (which itself defaults to `None`).


Custom path:
```python
load_dotenv(dotenv_path="/path/to/your/.env", override=False)
```
**`override` flag**:
- `False` (default): existing environment variables aren’t clobbered.
- `True`: always overwrite


>[!tip] Industry standard pattern
```python
import os
from sys import exit

# Required: crash early if missing
try:
    api_key = os.environ["SHOPIFY_API_KEY"]
    secret  = os.environ["SHOPIFY_SECRET"]
except KeyError as e:
    exit(f"Error: missing required env var {e}")

# Optional—with default/fallback
debug = os.getenv("DEBUG", "false").lower() == "true"
timeout = int(os.getenv("TIMEOUT", "30"))

```


|Mechanism|Role|Pros|Cons|
|---|---|---|---|
|**`os.environ` / `os.getenv`**|The built‑in bridge to your process’s environment variables.|• No extra dependencies• Full dict interface (`set`/`del`/`keys`)• Immediate, low‑level control|• All values are strings• No validation or type conversion• Must handle missing keys yourself|
|**`python‑dotenv`**|Reads a `.env` file (simple `KEY=VALUE` lines) and injects them into `os.environ`.|• Keeps secrets/config out of VCS• Supports variable expansion, defaults• Easy CLI integration (`dotenv run`)|• Still just strings in `os.environ`• You need extra code to validate or convert types|
|**Pydantic’s `BaseSettings`**|Declarative, type‑safe settings class that “pulls” from environment (and can read a `.env`).|• Automatic type conversion (e.g. `int`, `bool`, `List[str]`)• Built‑in validation, defaults, “required” enforcement• Clear schema in code• Supports nested settings models|• Adds a dependency on Pydantic• Slightly more boilerplate upfront|


---
### Linux 

```bash
# Viewing the current environment in shell
env           # lists all environment variables
printenv      # same as `env`
set           # lists shell variables *and* functions too
```

## “default” environment variables 

In **interactive** shell, several files are sourced in order, which set or export variables:

1. **System‑wide**
    - `/etc/environment`
    - `/etc/profile`
    - Anything in `/etc/profile.d/*.sh`
2. **User‑specific** (in home directory)
    - `~/.profile` (or on some systems `~/.bash_profile`)
    - `~/.bashrc` (for interactive non‑login shells)
    - `~/.bash_aliases` (if included `~/.bashrc`)

Each of those files can `export` variables. For example, many distros put a default `PATH` in `/etc/environment`