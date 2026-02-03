mflowgen
==========================================================================
[![Documentation Status](https://readthedocs.org/projects/mflowgen/badge/?version=latest)](https://mflowgen.readthedocs.io/en/latest) [![mflowgen pytest CI](https://github.com/watsondonald14/mflowgen/actions/workflows/pytest-ci.yml/badge.svg)](https://github.com/watsondonald14/mflowgen/actions/workflows/pytest-ci.yml) [![pypi](https://img.shields.io/pypi/v/mflowgen)](https://pypi.org/project/mflowgen)

A Python-based modular flow specification and build-system generator for ASIC and FPGA design-space exploration.

## Overview

mflowgen is a modular flow specification and build-system generator for ASIC and FPGA design-space exploration built around sandboxed and modular nodes.

mflowgen allows you to programmatically define and parameterize a graph of nodes (i.e., sandboxes that run anything you like) with well-defined inputs and outputs. Build system files (e.g., make, ninja) are then generated which shuttle files between nodes before running them.

<img width='350px' src='docs/_static/images/example-graph.jpg'>

## Key Features

- **Process and technology independence** -- Process technology libraries and variables can be abstracted and separated from physical design scripts
- **Sandboxed and modular nodes** -- Traditional ASIC flows composed of many steps with fixed dependencies become reusable, modular, and self-contained
- **Programmatically defined build-system generator** -- Python-based scripting with simple graph API for flexible node management
- **Run-time assertions** -- Built-in assertions checked at runtime with preconditions and postconditions
- **Hardware design-space exploration focus** -- Parameter expansion for parallel builds and design space characterization
- **Complete freedom in node definition** -- No restrictions on what nodes do aside from defining inputs/outputs

## Installation

### Requirements

- Python 3.6 or higher
- graphviz (for visualization)

### Quick Start

```bash
# Clone repository
git clone https://github.com/watsondonald14/mflowgen.git
cd mflowgen

# Install system dependencies
sudo apt-get install -y graphviz  # Ubuntu/Debian
# OR
brew install graphviz              # macOS

# Install Python dependencies
pip install -r requirements/ci.txt

# Install mflowgen
pip install .
```

## Basic Usage

### Running Demo

```bash
# Run demo setup
mflowgen run --demo

# Navigate to demo directory
cd mflowgen-demo && mkdir -p build && cd build

# Configure design
mflowgen run --design ../GcdUnit
```

### Common Commands

```bash
# List all available nodes
make list

# Check status of nodes
make status

# View runtime information
make runtimes

# Generate flow graph visualization
make graph

# Get detailed info
make info

# Clean all build artifacts
make clean-all
```

### Running Specific Nodes

```bash
# Run synthesis
make synopsys-dc-synthesis

# Run place and route
make cadence-innovus-place-route

# Run signoff
make synopsys-ptpx-power
```

### Running Subgraph Targets

For designs with subgraphs:

```bash
# Run specific subgraph node
make <subgraph-name>-<node-name>

# Check subgraph status
make <subgraph-name>-status
```

## Advanced Usage

### Custom Graph Configuration

Create a Python script to define your flow:

```python
from mflowgen.core import Graph

# Create new graph
g = Graph()

# Add nodes
g.add_node('synthesis', 'path/to/synthesis')
g.add_node('place-route', 'path/to/pnr')

# Connect nodes
g.connect_by_name('synthesis', 'place-route')

# Set parameters
g.set_param('synthesis', 'clock_period', 1.0)

# Build
g.build()
```

### Parameter Sweeps

```bash
# Run with custom parameters
mflowgen run --design MyDesign --graph-kwargs clock_period=1.0 area_target=1000
```

### Running Tests

```bash
# Run all tests
pytest tests/

# Run specific test
pytest tests/test_subgraph.py
```

## Included Flow Scripts

mflowgen ships with flow scripts for:

**Synthesis:**
- Synopsys Design Compiler (DC)
- yosys (open-source)

**Place and Route:**
- Cadence Innovus Foundation Flow
- RePlAce (open-source)
- graywolf (open-source)
- qrouter (open-source)

**Signoff:**
- Synopsys PrimeTime PX (PTPX)
- Mentor Calibre

**Design Kit:**
- Open-source 45nm ASIC Design Kit (ADK)
- FreePDK45 v1.4 + NanGate Open Cell Library

## Project Structure

```
mflowgen/
├── docs/                  # Documentation
├── mflowgen/             # Main package
│   ├── core/            # Core graph and node classes
│   ├── steps/           # Built-in flow steps
│   └── tests/           # Test suite
├── requirements/         # Python dependencies
└── examples/            # Example designs
```

## Troubleshooting

**Import errors:**
```bash
pip install -r requirements/ci.txt --force-reinstall
pip install .
```

**Graphviz not found:**
```bash
# Ubuntu/Debian
sudo apt-get install graphviz

# macOS
brew install graphviz
```

**Node execution fails:**
- Check node inputs exist
- Verify tool paths in configuration
- Review logs in node build directory

## Documentation

Full documentation: https://mflowgen.readthedocs.io/en/latest

## Development

### Code Quality Checks

```bash
# Format check
autoflake --recursive --in-place --remove-duplicate-keys .
pyupgrade --py3-only --keep-percent-format $(find . -name '*.py')

# Linting
flake8 --select=F --ignore=F401,F405,F403,F811,F821,F841
```

### TCL Script Guidelines

Every `[glob]` command must be preceded by `[lsort]` for deterministic behavior:

```tcl
# Bad - non-deterministic order
set lib_list [glob stdcells*.lib]

# Good - deterministic order
set lib_list [lsort [glob stdcells*.lib]]
```

## License

mflowgen is offered under the terms of the Open Source Initiative BSD 3-Clause License.

More information:
- http://choosealicense.com/licenses/bsd-3-clause
- http://opensource.org/licenses/BSD-3-Clause