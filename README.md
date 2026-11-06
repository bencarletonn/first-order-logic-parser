# First-Order Logic Parser

A Python tool that reads a first-order logic (FOL) formula together with the sets of symbols it is built from, generates the corresponding context-free grammar, and then parses the formula against that grammar, drawing the resulting parse tree.

Given a single input file, the program produces:

| Output           | Description                                                                 |
| ---------------- | --------------------------------------------------------------------------- |
| `grammar.txt`    | The generated grammar (production rules `P`, start symbol `S`, terminals `Vt`, non-terminals `Vn`) |
| `parse_tree.png` | A parse tree of the formula under that grammar                              |
| `log.txt`        | A log of the files created and any errors encountered                       |

## Requirements

- Python 3
- [`pydot`](https://pypi.org/project/pydot/)
- [Graphviz](https://graphviz.org/download/), which `pydot` uses to render the PNG and which must be on your `PATH`

```bash
pip install pydot
# macOS
brew install graphviz
# Debian/Ubuntu
sudo apt install graphviz
```

## Usage

```bash
python FOL-PARSER.py
```

When prompted, enter the name of your input file (including the extension):

```
Please enter a filename (including .txt): input.txt
```

The output files are written to the current directory. If the file does not exist, the program prints a message and exits.

## Input file format

The input file defines each set on its own labelled section, followed by the formula. The labels must appear in this order, and the items in each section are separated by whitespace:

```
variables:   ...
constants:   ...
predicates:  ...
equality:    ...
connectives: ...
quantifiers: ...
formula:     ...
```

| Section        | Contents                                                                                       |
| -------------- | ---------------------------------------------------------------------------------------------- |
| `variables:`   | Variable symbols                                                                               |
| `constants:`   | Constant symbols                                                                               |
| `predicates:`  | Predicate symbols, each with its arity in square brackets, e.g. `P[1]` or `R[2]`               |
| `equality:`    | The equality symbol                                                                            |
| `connectives:` | Five symbols. The first four are binary connectives, and the **fifth is treated as negation**  |
| `quantifiers:` | Quantifier symbols                                                                             |
| `formula:`     | The formula to parse, with symbols separated by spaces                                         |

An example input file:

![Example input file](https://user-images.githubusercontent.com/56607084/77767492-11111380-7039-11ea-93f1-74dee638c463.png)

## Generated grammar

If the input file is valid, a grammar for the language of well-formed formulae over those symbols is written to `grammar.txt`. The grammar has this general shape:

```
<variable>    ::= (your variables)
<constant>    ::= (your constants)
<term>        ::= <variable> | <constant>
<equality>    ::= (your equality symbol)
<negative>    ::= (your negation symbol)
<connective>  ::= (your binary connectives)
<quantifier>  ::= (your quantifiers)
<predicate>   ::= P(<term>) | R(<term>, <term>) | ...
<atom>        ::= <predicate> | (<term> <equality> <term>)
<formula>     ::= <atom> | (<formula> <connective> <formula>) | <negative><formula> | <quantifier><variable><formula>

S = { <formula> }
```

Predicate productions are expanded from their declared arity, so `R[2]` becomes `R(<term>, <term>)`. The file also lists the terminal set `Vt` and the non-terminal set `Vn`.

Here is the grammar generated from the example input:

![Generated grammar](https://user-images.githubusercontent.com/56607084/77767486-0eaeb980-7039-11ea-84dc-6e0e7e3d8364.png)

## Parse tree

The formula is then tokenised and parsed against the grammar, and the parse tree is rendered to `parse_tree.png`. It breaks the formula down into its grammatical structure, from `<formula>` at the root down to the terminal symbols at the leaves.

The parse tree for the example formula under the grammar above:

![Parse tree](https://user-images.githubusercontent.com/56607084/77767490-10787d00-7039-11ea-94df-12e67ae61050.png)

## Log file

Every run writes `log.txt` to the current directory. It records whether the input file and the formula were valid, and which files were generated. If the formula is invalid, the log gives the position of the offending symbol, the symbol that was received, and the symbols that were expected at that point.

![Log file](https://user-images.githubusercontent.com/56607084/77767489-0fdfe680-7039-11ea-987c-dfcdd47140b6.png)

## Error handling

- **File not found**: the program prints a message and exits.
- **Malformed input file**: for example, missing sections. The log records `The input file you have provided is INVALID.`
- **Invalid formula**: the log records `FORMULA: Invalid`, the position of the error, the received symbol, and the expected symbols.

## Project structure

```
FOL-PARSER.py   # the parser and grammar generator
README.md
```
