# DFEE: Interactive DataFlow Execution and Evaluation Kit

We explore program synthesis and execution for task-oriented dialogues in the context of personal time management (e.g., meeting scheduling). It aims to model goal-oriented tasks via interactive programing that is automatically constructed and directly executable on a client’s backend (e.g., personal calendar databases).

This package aims to provide:

1. A demonstration system that interacts with users using natural language and presents their calendars in real time.
2. A benchmark system that evaluates execution accuracy given expressions and the backend calendar.

## Installation

Run the following commands under the root folder of this project:

```bash
python3.7 -m venv venv
source  venv/bin/activate
pip install -e . --extra-index-url=https://smpypi.z5.web.core.windows.net/

```

- `--extra-index-url` is required to install `openfst-python` distributed by Microsoft.

Two modified opensource libraries are embedded. To download them 1. git clone the repository 2. git apply the provided patch:

### Open Pythonic Dataflow Dialogue Framework

This is a modified version of [telepathylabsai](https://github.com/telepathylabsai)/**[OpenDF](https://github.com/telepathylabsai/OpenDF)**, licensed under [MIT license](https://github.com/telepathylabsai/OpenDF/blob/main/LICENSE).

Modifications:

- Implemented 90 missing APIs in SMCalFlow.
- Many bug fixes.

```bash
git clone https://github.com/telepathylabsai/OpenDF.git
cd OpenDF
git apply ../opendf_changes.patch
mv opendf ../src/.
cd ..
```

### Semantic Parsing with Constrained Language Models

This is a modified version of [microsoft](https://github.com/microsoft)/**[semantic_parsing_with_constrained_lm](https://github.com/microsoft/semantic_parsing_with_constrained_lm)**, [MIT license](https://github.com/microsoft/semantic_parsing_with_constrained_lm/blob/main/LICENSE).

Modifications:

- Implemented `semantic_parsing_with_constrained_lm.finetune.platypus`, see https://github.com/microsoft/semantic_parsing_with_constrained_lm/issues/13
- Supported SMCalFlow V1 and Simplified syntax.

```bash
git clone https://github.com/microsoft/semantic_parsing_with_constrained_lm.git
cd semantic_parsing_with_constrained_lm
git apply ../semantic_parsing_with_constrained_lm_changes.patch
mv semantic_parsing_with_constrained_lm ../src/.
cd ..
```

- This codebase has been tested on `Deep Learning AMI GPU PyTorch 1.11.0 (Amazon Linux 2) 20220526` and MacOS with Python 3.7. 
- Other OS or Python versions (shipped with Conda) have not been tested and might not work properly.

To deploy the GUI, you need to install `nginx` and `npm` then:

- Copy the config file `gui/nginx.conf` to `/etc/nginx/nginx.conf`
- Install and build the GUI package:

```bash
cd gui
npm install
npm run build
```

- Copy `dist` to  `/usr/share/nginx/html` :

```bash
sudo cp -rf gui/* /usr/share/nginx/html
```

## Parser

Before running any other sub-systems, please start the parser sever which is required for other sub-systems. The parser models should be downloaded and unzipped to `data/models/trained_models/1.0/`.

```bash
python src/aws_lex_program_synthesis_calendar/parser_server.py
```

## Demonstration

We provide both CLI and GUI for demonstration.

### CLI

Run the following command to start a calendar bot in your terminal:

```bash
python src/aws_lex_program_synthesis_calendar/bot_cli.py
```

### GUI

Run the following command to start a calendar bot on the web:

```bash
python src/aws_lex_program_synthesis_calendar/bot_server.py
```

Then, start the UI server:

```bash
cd gui
npm install        # first-time installation
npm run dev
```

If you plan to host it on a file server (e.g., nginx), you can build a distribution version:

```bash
npm run build
```

Then copy the dist folder to your file server.

## Benchmarking

The dialogs for benchmarking are in markdown format, e.g., `data/benchmark/tiny_samples/0001.md`. 

To evaluate on a single file:

```bash
python src/aws_lex_program_synthesis_calendar/benchmark_cli.py data/benchmark/tiny_samples/0001.md
```

To evaluate multiple files:

```bash
python src/aws_lex_program_synthesis_calendar/benchmark_cli.py data/benchmark/tiny_samples/*.md
```

## Security

See [CONTRIBUTING](CONTRIBUTING.md#security-issue-notifications) for more information.

## License

This library is licensed under the Apache 2.0 License. See the LICENSE file.
