# Kvitynsky
# This is a basic workflow that is manually triggered

name: Manual workflow

# Controls when the action will run. Workflow runs when manually triggered using the UI
# or API.
on:
  workflow_dispatch:
    # Inputs the workflow accepts.
    inputs:
      name:
        # Friendly description to be shown in the UI instead of 'name'
        description: 'Person to greet'
        # Default value if no value is explicitly provided
        default: 'Executer'
        # Input has to be provided for the workflow to run
        required: true
        type: string

# A workflow run is made up of one or more jobs that can run sequentially or in parallel
jobs:
  # This workflow contains a single job called "greet"
  greet:
    name: Simple manual trigger
    # The type of runner that the job will run on
    runs-on: ubuntu-latest
    strategy:
      matrix:
        python: ["3.10"]

    # Steps represent a sequence of tasks that will be executed as part of the job
    steps:
    # Runs a single command using the runners shell
    - uses: actions/checkout@v3
    - name: Send greeting
      run: echo "Hello ${{ github.event.inputs.name }}"
    - name: Set-up Python
      uses: actions/setup-python@v4.3.0
      with:
        python-version: ${{ matrix.python }}
      if: github.event.inputs.name != 'Executer'
    - name: Get Python Version
      if: github.event.inputs.name != 'Executer'
      run: |
        python --version
        python -c 'import datetime; print(f"This Workflow start at: {datetime.datetime.now()}")'
  goodbye:
    name: Say goodbye
    runs-on: ubuntu-latest
    strategy:
      matrix:
        python: ["3.10"]

    # Steps represent a sequence of tasks that will be executed as part of the job
    steps:
    # Runs a single command using the runners shell
    - uses: actions/checkout@v3
    - name: Say goodbye
      if: github.event.inputs.name == 'Executer'
      run: echo "Goodbye ${{ github.event.inputs.name }}"



# This is a basic workflow that is manually triggered

name: Coverage workflow

# Controls when the action will run. Workflow runs when manually triggered using the UI
# or API.
on:
  workflow_dispatch:
    # Inputs the workflow accepts.
    inputs:
      name:
        # Friendly description to be shown in the UI instead of 'name'
        description: 'Who start it'
        # Default value if no value is explicitly provided
        default: 'Executer'
        # Input has to be provided for the workflow to run
        required: true
        type: string

# A workflow run is made up of one or more jobs that can run sequentially or in parallel
jobs:
  # This workflow contains a single job called "greet"
  coverage:
    name: Run test and get coverage
    # The type of runner that the job will run on
    runs-on: ubuntu-latest
    strategy:
      matrix:
        python: ["3.10"]

    # Steps represent a sequence of tasks that will be executed as part of the job
    steps:
    # Завантажуємо останню версію коду
    - uses: actions/checkout@v3
    # Ініціалізуємо Python
    - name: Set-up Python
      uses: actions/setup-python@v4.3.0
      with:
        python-version: ${{ matrix.python }}
    # встановлюємо Python пакети
    - name: Install dependencies
      run: |
        pip install pytest coverage
    # запускаємо тести
    - name: Run test
      run: |
        cd example/coverage_io/
        pytest test.py -v
    # генеруємо звіт
    - name: Generate Report
      run: |
        cd example/coverage_io/
        coverage run -m unittest -v
        coverage xml
    # відсилаємо його в Coverage
    - name: Upload Coverage to Codecov
      uses: codecov/codecov-action@v3
      with:
        directory: example/coverage_io/   
