# Warehouse control

A command-line inventory program in plain Python: register items, list the catalog and report on stock, saving the catalog to a file with `pickle`.

## Context

I wrote this in 2020 while learning Python, before I knew the conventions for structuring a program or handling errors. Despite the repository name, it doesn't use Django; it's a terminal script. I keep it public as a record of how I learned.

## Stack

- Python 3 standard library only (`pickle`, `os`, `datetime`)
- The `__pycache__` files that were committed came from CPython 3.8; the stray `.pyc` files next to the source were Python 2.7 bytecode.
- No third-party packages, so there's no `requirements.txt`.

## Running it

    cd warehouse
    python3 warehouse.py

It won't start as committed: `chepest_product` (`warehouse.py:138-147`) is missing a colon and has broken indentation, which is a `SyntaxError` for the whole file. Run it from `warehouse/`, because the data file path is relative. There are no environment variables.

## Status

With that function fixed or removed, options 2 (catalog), 3 (out of stock), 4 (stock value), 5 (update price) and 10 (three highest prices) run. The others fail as listed below. `warhouse.data` holds an empty catalog, which the loader reports as "Error, data not loaded".

## Known gaps

- `warehouse.py:143`: missing colon after the `if`. The lines after it (144-147) are mis-indented, and line 147 prints an undefined name, `chepest`.
- `warehouse.py:192`: option 1 references `register_item` without calling it, so no item is registered. The catalog is still saved.
- `warehouse.py:51-68`: if input is invalid, the error is printed but the code still builds the item with undefined variables.
- `warehouse.py:111, 119`: `display_catalog("")` passes an argument the function doesn't accept (`TypeError`), so options 6 and 7 crash.
- `warehouse.py:115`: `item.remove(...)` calls a method `Item` doesn't have; it should remove the item from `catalog`. Line 124 asks for the new stock but never saves it.
- `warehouse.py:126`: `found` is only set inside the `if`, so it can be undefined.
- `warehouse.py:134`: `item.category` doesn't exist (the attribute is `catagory`), so option 8 crashes.
- `warehouse.py:139`: `print_header()` is called without its required argument. Line 217 calls `cheapest_product()`, but the function is named `chepest_product`, so option 9 crashes.
- `warehouse.py:93`: the total is printed inside the loop, once per item. Lines 156-158 print prices, not the products, and fail with fewer than three items.
- `warehouse.py:48`: a bare `except` hides the real error when the data file fails to load.
- `menu.py:27-33`: on macOS and Linux, `clear()` runs an empty command, so the screen is never cleared.
- `item.py:7`: the class attribute is spelled `prce`; instances use `price`.
- Saves only happen after option 1, so price changes are lost on exit. Unpickling can run arbitrary code, so only load data files you trust.
- No tests. Committed bytecode is untracked now but remains in history.

## What I'd do differently now

- Run every menu option after a change, or write a small test for each.
- Validate input in one place and keep going with a clear message instead of crashing.
- Save to JSON or SQLite instead of pickle, and save after every change.
