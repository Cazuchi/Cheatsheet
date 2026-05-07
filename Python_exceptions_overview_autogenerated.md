# Python exceptions overview

---

### ArithmeticError
Base class for `ZeroDivisionError`, `OverflowError`, and `FloatingPointError`. Catch this if you want to handle any arithmetic failure without listing each subclass individually.

```python
try:
    result = some_numeric_operation()
except ArithmeticError as e:
    print(f"Arithmetic operation failed: {e}")
```

---

### AssertionError
Raised when an `assert` statement fails — i.e. the condition evaluated to `False`.

```python
try:
    assert len(df) > 0, "DataFrame is empty"
except AssertionError as e:
    print(f"Assertion failed: {e}")
```

---

### AttributeError
Raised when accessing or setting an attribute that doesn't exist on an object. Common when a method returns `None` and you try to chain a call on it.

```python
try:
    city_dict.update({elem.find('code').text: elem.find('label').text})
except AttributeError:
    pass  # elem.find() returned None — no 'code' or 'label' child element
```

---

### BaseException
The root base class for *all* exceptions, including system exits. Almost never catch this — it will swallow `KeyboardInterrupt` and `SystemExit`, making your program impossible to stop cleanly.

```python
try:
    run_pipeline()
except BaseException as e:
    print(f"Something went very wrong: {e}")
    raise  # Almost always re-raise if you catch this at all
```

---

### BrokenPipeError
Raised when writing to a pipe or socket whose other end has been closed. Subclass of `OSError`.

```python
try:
    process.stdin.write(data)
except BrokenPipeError as e:
    print(f"Pipe closed before write completed: {e}")
```

---

### BufferError
Raised when a buffer operation cannot be performed — for example, trying to resize a buffer that is being referenced elsewhere.

```python
try:
    buffer_operation()
except BufferError as e:
    print(f"Buffer error: {e}")
```

---

### ConnectionError
Base class for network connection problems. Subclass of `OSError`. Covers `ConnectionRefusedError`, `ConnectionResetError`, and `BrokenPipeError`.

```python
try:
    response = requests.get(url)
except ConnectionError as e:
    print(f"Network connection failed: {e}")
```

---

### ConnectionRefusedError
Raised when a connection attempt is refused by the remote end. Subclass of `ConnectionError`.

```python
try:
    socket.connect((host, port))
except ConnectionRefusedError as e:
    print(f"Connection refused by {host}:{port} — is the server running? {e}")
```

---

### ConnectionResetError
Raised when a connection is reset by the remote end mid-session. Subclass of `ConnectionError`.

```python
try:
    data = socket.recv(1024)
except ConnectionResetError as e:
    print(f"Connection was reset by the remote host: {e}")
```

---

### DeprecationWarning
Raised when using deprecated features. Only visible if warnings are enabled — use `warnings.warn()` to trigger it yourself, or `warnings.filterwarnings()` to surface it.

```python
import warnings
try:
    with warnings.catch_warnings():
        warnings.simplefilter("error", DeprecationWarning)
        legacy_function()
except DeprecationWarning as e:
    print(f"Deprecated feature used: {e}")
```

---

### EOFError
Raised when `input()` reaches end-of-file without reading any data. Common in scripts that read from stdin in a pipeline.

```python
try:
    user_input = input("Enter value: ")
except EOFError:
    print("No input received — stdin closed.")
```

---

### Exception
Base class for all non-system-exiting exceptions. Use as a broad last-resort catch, but prefer more specific exceptions where possible. Intentionally excludes `KeyboardInterrupt` and `SystemExit`.

```python
try:
    run_pipeline()
except Exception as e:
    print(f"Unexpected error: {e}")
    raise  # Re-raise if you want the error to still propagate after logging
```

---

### FileExistsError
Raised when trying to create a file or directory that already exists. Subclass of `OSError`.

```python
try:
    os.makedirs(output_dir)
except FileExistsError:
    pass  # Directory already exists — fine to continue
```

---

### FileNotFoundError
Raised when a file or directory does not exist. Subclass of `OSError`.

```python
try:
    with open("path.json", "r") as f:
        config = json.load(f)
except FileNotFoundError as e:
    print(f"Config file not found: {e}")
```

---

### FloatingPointError
Raised when a floating-point operation fails. Rarely raised in standard Python unless the `fpectl` module is enabled.

```python
try:
    result = float_operation()
except FloatingPointError as e:
    print(f"Floating point error: {e}")
```

---

### FutureWarning
Raised to warn that a feature's behaviour will change in a future version of Python or a library.

```python
import warnings
try:
    with warnings.catch_warnings():
        warnings.simplefilter("error", FutureWarning)
        potentially_changing_function()
except FutureWarning as e:
    print(f"Future compatibility warning: {e}")
```

---

### GeneratorExit
Raised inside a generator when `.close()` is called on it. Subclass of `BaseException`, not `Exception`. Usually should not be caught — and if it is, it must not be suppressed.

```python
def my_generator():
    try:
        while True:
            yield value
    except GeneratorExit:
        print("Generator is being closed — run cleanup here")
        raise  # Must re-raise or return — never suppress GeneratorExit
```

---

### ImportError
Raised when a module cannot be imported — e.g. it exists but has an error in it, or a specific name can't be found within it.

```python
try:
    from some_module import some_function
except ImportError as e:
    print(f"Could not import required function: {e}")
```

---

### IndentationError
Raised when Python encounters incorrect indentation. Subclass of `SyntaxError`. Occurs at parse time, not runtime.

```python
try:
    exec(code_string)
except IndentationError as e:
    print(f"Indentation error in code string at line {e.lineno}: {e.msg}")
```

---

### IndexError
Raised when a sequence (list, tuple, string) is accessed with an index that is out of range.

```python
try:
    first_item = my_list[0]
except IndexError:
    print("List is empty — no item at index 0.")
```

---

### IOError
Old alias for `OSError` kept for backwards compatibility. In modern Python (3.3+) it is identical to `OSError`. Prefer `OSError` in new code.

```python
try:
    with open(filepath, "r") as f:
        data = f.read()
except IOError as e:
    print(f"IO error: {e}")
```

---

### IsADirectoryError
Raised when a file operation is attempted on a path that is a directory. Subclass of `OSError`.

```python
try:
    with open(path, "r") as f:
        data = f.read()
except IsADirectoryError as e:
    print(f"Expected a file but got a directory: {e}")
```

---

### KeyboardInterrupt
Raised when the user presses Ctrl+C. Subclass of `BaseException`, not `Exception`, so it is not caught by `except Exception`. Only catch this if you need to run cleanup before exiting.

```python
try:
    run_long_pipeline()
except KeyboardInterrupt:
    print("Pipeline interrupted by user — cleaning up...")
    cleanup()
```

---

### KeyError
Raised when a dictionary key does not exist.

```python
try:
    value = my_dict[key]
except KeyError:
    print(f"Key '{key}' not found in dictionary.")
```

---

### LookupError
Base class for `IndexError` and `KeyError`. Catch this if you want to handle any failed lookup without caring whether it was an index or a key.

```python
try:
    item = data_structure[identifier]
except LookupError as e:
    print(f"Lookup failed: {e}")
```

---

### MemoryError
Raised when Python runs out of memory to allocate. Difficult to handle gracefully — at minimum, log it and exit cleanly.

```python
try:
    large_df = pd.read_csv(huge_file)
except MemoryError:
    print("Not enough memory to load this file — consider chunking.")
```

---

### ModuleNotFoundError
Raised when an import fails because the module simply does not exist. Subclass of `ImportError`.

```python
try:
    import optional_library
except ModuleNotFoundError:
    print("optional_library is not installed — run: pip install optional_library")
```

---

### NameError
Raised when referencing a variable name that has not been defined in the current scope.

```python
try:
    print(undefined_variable)
except NameError as e:
    print(f"Variable not defined: {e}")
```

---

### NotADirectoryError
Raised when a directory operation is attempted on something that is not a directory. Subclass of `OSError`.

```python
try:
    os.listdir(path)
except NotADirectoryError as e:
    print(f"Path is not a directory: {e}")
```

---

### NotImplementedError
Raised when an abstract method is called without being implemented in a subclass. Also used as a placeholder in code that is intentionally incomplete.

```python
try:
    result = base_class_instance.process()
except NotImplementedError:
    print("This method must be implemented in a subclass.")
```

---

### OSError
Base class for all OS-related errors. Also aliased as `IOError` and `EnvironmentError`. Covers file, permission, network, and other system-level errors. Catching `OSError` also catches all its subclasses.

```python
try:
    os.remove(filepath)
except OSError as e:
    print(f"OS error when deleting file: {e}")
```

---

### OverflowError
Raised when a numeric result is too large to be represented. Most common with floats — Python integers themselves can grow arbitrarily large.

```python
try:
    result = math.exp(10000)
except OverflowError:
    print("Result is too large to represent as a float.")
```

---

### PermissionError
Raised when an operation lacks the required permissions. Subclass of `OSError`.

```python
try:
    with open("/etc/protected_file", "w") as f:
        f.write(data)
except PermissionError as e:
    print(f"Insufficient permissions: {e}")
```

---

### RecursionError
Raised when the maximum recursion depth is exceeded — i.e. a function calls itself too many times without returning.

```python
try:
    result = deeply_recursive_function(data)
except RecursionError:
    print("Recursion limit hit — consider an iterative approach.")
```

---

### ReferenceError
Raised when a weak reference is used after the object it refers to has been garbage collected.

```python
import weakref
try:
    value = weak_ref()
    if value is None:
        raise ReferenceError("Referent has been garbage collected")
except ReferenceError as e:
    print(f"Weak reference is no longer valid: {e}")
```

---

### ResourceWarning
Raised when a resource (file, socket) is not properly closed and is garbage collected with the connection still open. Only shown when warnings are enabled.

```python
import warnings
warnings.simplefilter("always", ResourceWarning)
try:
    f = open(filepath)  # Opened but never closed — will trigger ResourceWarning at GC
except ResourceWarning as e:
    print(f"Resource not closed properly: {e}")
```

---

### RuntimeError
Raised for generic runtime errors that don't fit a more specific category. Also used by some libraries as a fallback.

```python
try:
    result = some_operation()
except RuntimeError as e:
    print(f"Runtime error: {e}")
```

---

### RuntimeWarning
Raised for dubious but not necessarily wrong runtime behaviour — e.g. numpy division by zero producing `inf` or `nan`.

```python
import warnings
try:
    with warnings.catch_warnings():
        warnings.simplefilter("error", RuntimeWarning)
        result = np.sqrt(-1)
except RuntimeWarning as e:
    print(f"Runtime warning treated as error: {e}")
```

---

### StopAsyncIteration
Raised by `__anext__()` when an async iterator is exhausted. The `async for` loop handles this automatically — you would only catch it in manual async iteration.

```python
async def consume():
    try:
        value = await async_iterator.__anext__()
    except StopAsyncIteration:
        print("Async iterator exhausted.")
```

---

### StopIteration
Raised by `next()` when an iterator has no more items. The `for` loop handles this automatically — you would only catch it when calling `next()` manually.

```python
try:
    value = next(my_iterator)
except StopIteration:
    print("Iterator is exhausted — no more items.")
```

---

### SyntaxError
Raised when the Python parser encounters invalid syntax. Happens at parse/compile time, not at runtime — unless you are using `exec()`, `eval()`, or `compile()`.

```python
try:
    exec(user_provided_code)
except SyntaxError as e:
    print(f"Invalid syntax at line {e.lineno}: {e.msg}")
```

---

### SystemError
Raised when the Python interpreter itself encounters an internal error. Extremely rare in normal code — if you see this, it is likely a bug in a C extension.

```python
try:
    c_extension_call()
except SystemError as e:
    print(f"Internal interpreter error — possible bug in a C extension: {e}")
```

---

### SystemExit
Raised by `sys.exit()`. Subclass of `BaseException`, not `Exception`. Only catch this if you need to perform cleanup before the process exits — and always re-raise or let it propagate.

```python
try:
    sys.exit(1)
except SystemExit as e:
    print(f"Exiting with code {e.code} — running cleanup first.")
    cleanup()
    raise
```

---

### TabError
Raised when indentation uses a mix of tabs and spaces inconsistently. Subclass of `IndentationError`.

```python
try:
    exec(code_string)
except TabError as e:
    print(f"Mixed tabs and spaces at line {e.lineno}.")
```

---

### TimeoutError
Raised when a system-level operation times out. Subclass of `OSError`. Note: this is the built-in — libraries like `requests` have their own timeout exceptions.

```python
try:
    result = socket_operation()
except TimeoutError as e:
    print(f"Operation timed out: {e}")
```

---

### TypeError
Raised when an operation is applied to an object of the wrong type — e.g. adding a string to an integer, or calling a non-callable.

```python
try:
    result = value + 1
except TypeError as e:
    print(f"Type mismatch: {e}")
```

---

### UnboundLocalError
Raised when a local variable is referenced before it has been assigned a value. Subclass of `NameError`. Common when a variable is conditionally assigned but then used unconditionally.

```python
try:
    print(result)  # 'result' only assigned inside an if block that didn't execute
except UnboundLocalError as e:
    print(f"Variable used before assignment: {e}")
```

---

### UnicodeDecodeError
Raised when a byte string cannot be decoded using the specified encoding. Subclass of `UnicodeError`.

```python
try:
    text = byte_string.decode("utf-8")
except UnicodeDecodeError:
    text = byte_string.decode("ISO-8859-1")  # Fallback encoding
```

---

### UnicodeEncodeError
Raised when a string cannot be encoded using the specified encoding. Subclass of `UnicodeError`.

```python
try:
    encoded = text.encode("ascii")
except UnicodeEncodeError:
    encoded = text.encode("utf-8")  # Fallback to a broader encoding
```

---

### UnicodeError
Base class for `UnicodeDecodeError`, `UnicodeEncodeError`, and `UnicodeTranslateError`. Catch this to handle any Unicode conversion failure.

```python
try:
    result = process_text(raw_bytes)
except UnicodeError as e:
    print(f"Unicode conversion failed: {e}")
```

---

### UnicodeTranslateError
Raised when a string cannot be translated during a character mapping operation. Subclass of `UnicodeError`. Less common than encode/decode errors.

```python
try:
    translated = text.translate(mapping_table)
except UnicodeTranslateError as e:
    print(f"Character translation failed: {e}")
```

---

### UserWarning
The default category for `warnings.warn()` when no specific category is given.

```python
import warnings
try:
    with warnings.catch_warnings():
        warnings.simplefilter("error", UserWarning)
        warnings.warn("Something looks off but isn't fatal")
except UserWarning as e:
    print(f"Warning treated as error: {e}")
```

---

### ValueError
Raised when a function receives an argument of the correct type but with an inappropriate value — e.g. `int("abc")` or `math.sqrt(-1)`.

```python
try:
    number = int(user_input)
except ValueError:
    print(f"'{user_input}' cannot be converted to an integer.")
```

---

### ZeroDivisionError
Raised when dividing or performing modulo by zero.

```python
try:
    ratio = numerator / denominator
except ZeroDivisionError:
    ratio = None  # Or 0, or float('inf') — depending on context
```
