# hurl-run: A script to run hurl file with extra functionalities

## What does this script do

* Attach extra headers for all requests
* Interpolate header names and header values using Python functions
* Define your own Python functions and use them in interpolations
* Set active environment in a file

## How

### Set active environment

1. Create directory `envs` in the project folder, and put variables in `envs/<environment>.env`. The format of the file is identical to `hurl`'s `--variables-file`.
   * Example: `local` environmental variables should be saved in `envs/local.env`.
   * Example: `dev` environmental variables should be saved in `envs/dev.dev`.
2. Create file `.helper/env`, and in the file, indicate the name of the active environment.
   * Example: if `.helper/env`'s content is `local`, all variables defined in `env/local.env` will be used in the HTTP request.

### Define your own Python functions

1. Create file `.helper/extra-functions.py` in the project folder.
2. Define your Python functions. You are free to import any built-in packages.

Example: Define a function to create a random UUID

```python
import uuid
def random_uuid():
    return str(uuid.uuid4())
```

### Add default headers

1. Create file `.helper/default-headers.json` in the project folder.
2. Define an object in the json file where:
   1. key-value pairs defined in base object are the headers for all environments.
   2. key-value pairs could be defined in a nested object, which means they are for a specific environments.
3. You can use `%%python{<function call>}%%` to call the functions defined in `.helper/extra-functions.py`.

Example: Define default headers

```json
{
  "Header1": "Value1",
  "Header2": "Value2",
  "local": {
    "Header1": "LocalValue1",
    "Header3": "LocalValue3"
  },
  "dev": {
    "Header2": "DevHeader2",
    "UUID": "%%python{random_uuid()}"
  }
}
```

In this example, if `local` environment is being used, headers will be sent as:
```text
Header1: LocalValue1
Header2: Value2
Header3: LocalValue3
```
if `dev` environment is being used, headers will be sent as:
```text
Header1: Value1
Header2: DevHeader2
UUID: <Result of function call random_uuid()>
```
For each request, the function is called separately.