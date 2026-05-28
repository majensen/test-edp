# Test EDP functionality

This a quick user acceptance testing project for the Extended Definition Property
functionality. It integrates enhancements to packages [bento-meta](https://github.com/CBIIT/bento-meta/python),
[bento-mdf](https://github.com/CBIIT/bento-mdf/python), and [bento-sts](https://github.com/CBIIT/bento-sts-fastapi),
plus updates to the MDB schema and content.

## Flow

The basic flow is: 

* Read MDF files with bento_mdf.mdf.MDFReader (a.k.a, MDF)
* MDFReader parses MDF into a bento_meta.Model
* When bento_meta identifies an EDP as a property Enum, it queries the (new) /edp bento-sts endpoint to obtain the actual PVs
* Just use the "prop.terms" attribute via the Model to get the list of terms, as usual.


## Install

* Get [uv](https://docs.astral.sh/uv/) if you don't have it, by running
```
curl -LsSf https://astral.sh/uv/install.sh | sh
```
or on Windows
```
PS> powershell -c "irm https://astral.sh/uv/install.ps1 | more"
```

* Clone this project and cd to the test-edp directory.

* Create a virtual environment:

```
uv venv
```

* Install with
```
uv pip install .
```
This should automatically install bleeding edge packages from their git branches into the virtual environment.


## Set up STS

To start an STS pointing to a suitable MDB:

* Edit /env.eg, setting ...URI, ...USER, ...PASS to access a suitable MDB instance, then save as ".env" in the test-edp directory

* Start the service in a separate terminal: cd to the test-edp directory and run
```
uv run uvicorn bento_sts.sts:app
```
It will continue running, emitting log messages.

* Visit http://localhost:8000/docs - Swagger documentation should appear.

## Try bento-mdf

* In another terminal, cd to the test-edp directory and use uv to start a Python shell.
```
uv run python
```
(This ensures that the shell uses the virtual environment where things were installed.)

* Try the following
```
from bento_mdf.mdf import MDF
mdf = MDF("test-model-edp-enum.yml")
model = mdf.model
terms = model.nodes['participant'].props['race'].terms
print( [x.handle for x in terms] )
```

GLHF
