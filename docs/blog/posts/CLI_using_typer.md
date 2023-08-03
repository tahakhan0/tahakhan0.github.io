---
title: Writing CLI tools using Typer
date: "2021-05-15"
description: "See how easy it is to combine python functions and typer's decorators to create cli tools."
---

## Background

I was given a task to write a cli (command line interface) tool in python. Naively, I started with [argparse](https://docs.python.org/3/library/argparse.html). Which is an awesome library but not beginner friendly. Luckily my mentor/manager stopped me at the right time and introduced me to [Typer](https://typer.tiangolo.com/).

Typer is a library written by [Sebastián Ramírez](https://twitter.com/tiangolo?lang=en) and it makes it really easy to write CLI tools.

It is simple and easy to learn because it lets you write python functions using Typer's decorators. Meaning you can write CLI tools if you know basic python functions.

## Project

I'm going to explain what I've learned by creating a CLI tool that parses a CSV file obtained from National Records of Scotland [here](https://www.nrscotland.gov.uk/statistics-and-data/statistics/statistics-by-theme/vital-events/names/babies-first-names/babies-first-names-summary-records-comma-separated-value-csv-format)
. It contains ~4000 records with first_name, last_name, number, rank, and sex. 

Link to git repo [here](https://github.com/tahakhan0/CLI-Tool)

The repo contains extensive documentation on how to run commands and what to expect by each of it. However with this post, I will be breaking down the how's/what's/when's of the commands.

For example:

#### Let's take the first command, `read`.

It is a python function that uses a decorator with some inputs.

```python
@app.command()
def read(path: Path = typer.Argument(None)):
    """
    1. Name of the command: read
    2. Inputs
        a. path:
        Description: Required input (therefore Argument).
                     By default set to None.
        Type: Path
        Short version: N/A
    """
```

Usage:

```python
$ cd src

python main.py read ../babies_names.csv 
```

<br/>

#### Now, the second command, `find`.

```python
@app.command()
def find(
    path: Path = typer.Argument(None),
    fields: Optional[List[str]] = typer.Option(None, "--f"),
    write_path: Path = typer.Option(None,"--w"),
):
    """
    1. Name of the command: find
    2. Inputs
        a. path:
           Description: Required input (therefore Argument).
                        By default set to None.
           Type: Path
           Short version: N/A

        b. fields:
           Description: An optional list of strings. By default
                        set to None.
           Type: List of strings.
           Short version: --f

        c. write_path:
           Description: Optional input (therefore Argument).
                        By default set to None.
           Type: Path
           Short version: --w

    """
```

Usage:

```python
$ cd src

python main.py find ../babies_names.csv --f year --f sex --w ../write_new_file.csv
```

That should be more than enough to get you started. For an in-depth explanation please take a look at the [repo](https://github.com/tahakhan0/CLI-Tool).
 

If you have any questions/comments/suggestions reach out to me via [twitter](https://twitter.com/tahaaakhan)
