# 02
name: integration

on:
  push:
    branches:
      - master
  pull_request:
    branches:
      - master

jobs:

  checks:
    runs-on: ubuntu-latest
    strategy:
      max-parallel: 6
      matrix:
        check: [bluecheck, doc8, docs, flake8, isortcheck, rstcheck]
    steps:
    - uses: actions/checkout@v3
    - uses: actions/setup-python@v4
      with:
        python-version: '3.10'
    - name: Setup tox
      run: |
        pip install --upgrade pip
        pip install tox
    - name: Run checks with tox
      run: |
        tox -e ${{ matrix.check }}

  tests:
    needs: checks
    runs-on: ${{ matrix.os }}
    strategy:
      max-parallel: 5
      matrix:
        os: [ubuntu-latest]
        python-version: [3.7, 3.8, 3.9, '3.10', 3.11]
    steps:
      - uses: actions/checkout@v3
      - uses: actions/setup-python@v4
        with:
          python-version: ${{ matrix.python-version }}
      - name: Setup tox
        run: |
          pip install --upgrade pip
          pip install tox
      - name: Test with tox
        run: tox -e py
