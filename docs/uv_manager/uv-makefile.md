# uv and Makefile

uv works well with Makefiles because Make can provide short aliases for common uv commands.

Example:

```makefile
install:
	uv sync

run:
	uv run python main.py

test:
	uv run pytest

lint:
	uv run ruff check .

format:
	uv run ruff format .

update:
	uv lock --upgrade
	uv sync

clean:
	rm -rf .venv
```

Then instead of remembering:

```bash
uv run pytest
```

you can use:

```bash
make test
```

Or:

```bash
make install
make test
make lint
make format
```

A useful project workflow becomes:

```text
Makefile
   │
   ├── install ──► uv sync
   ├── test ─────► uv run pytest
   ├── lint ─────► uv run ruff
   └── run ──────► uv run ...
```

This is particularly useful in teams because developers and CI can use the same commands.

---