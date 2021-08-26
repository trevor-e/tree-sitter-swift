# experimental-tree-sitter-swift

This contains an experimental [`tree-sitter`](https://tree-sitter.github.io/tree-sitter) grammar for the Swift
programming language. The grammar can generate a parser that is able to parse most basic Swift code.

There are some known correctness issues in code that the generated parser claims to successfully parse, which are found
under the label [ast-incorrect](https://github.com/alex-pinkus/experimental-tree-sitter-swift/labels/ast-incorrect). The
parser was built with a priority on _generating an AST without errors_, with the correctness of the syntax tree taking
second priority. If you'd like to use it for your use cases, and the correctness issues prevent you from doing so, pull
requests are welcomed.

## Getting started

This grammar is structured similarly to any other tree-sitter grammar. As with most grammars, you can use the cli to
generate or test the parser, and to parse any arbitrary code. An overview of the CLI tool can be found
[here](https://tree-sitter.github.io/tree-sitter/creating-parsers#tool-overview). A moderate amount of work has gone
into building out a corpus, but the corpus mostly encodes the current state of the parser, including a few bugs that it
has.

With this package checked out, a common workflow for editing the grammar will look something like:

1. Make a change to `grammar.js`.
2. Run `tree-sitter generate && tree-sitter test` to see whether the change has had impact on existing parsing behavior.
3. Run `tree-sitter parse` on some real Swift codebase and see whether (or where) it fails.
4. Use any failures to create new corpus test cases.

If you simply want to _use_ the parser, and not modify it, you can depend on it from Rust code using the instructions in
the [rust bindings README](https://github.com/tree-sitter/tree-sitter/blob/master/lib/binding_rust/README.md).
Basically, you'll want to build the `parser.c` and `scanner.c` in your build script and use the generated files to
produce a parser that the bindings can interact with. I don't plan to publish this as a crate while it's still in the
`experimental` state.

## Contributions

If you have a change to make to this parser, and the change is a net positive, please submit a pull request. I mostly
started this parser to teach myself how `tree-sitter` works, and how to write a grammar, so I welcome improvements. If
you have an issue with the parser, please file a bug and include a test case to put in the `corpus`. I can't promise any
level of support, but having the test case makes it more likely that I want to tinker with it.

## Frequently asked questions

### Isn't there already a Swift grammar? Why did you create another one?

There is a Swift grammar that lives under the `tree-sitter` org, but it doesn't have a `LICENSE` file. Since it's not
licensed, I would have to be cautious about using it myself. I actually haven't looked at the code itself, or the
repository much at all, since I want to avoid potential licensing issues with this parser. A brief scan of the Github
issues tells me it's also not quite complete, but I haven't done a deep dive to see whether it's more complete than this
one.

I created this parser to learn how to use tree-sitter. Looking at the landscape, it seemed like it might be useful to
others, so I decided to publish it. Since this has a permissive license, it seems like a good path forward for anyone
else whose needs are similar to my own.

### If this isn't a fork of the existing Swift grammar, what is it based on?

A lot of specific features were based off of the [Kotlin language grammar](https://github.com/fwcd/tree-sitter-kotlin),
since the two have many cosmetic similarities. For instance, both languages have trailing closure syntax. Some parts
also come from the [Rust grammar](https://github.com/tree-sitter/tree-sitter-rust), which this grammar should probably
copy more of.

### Why is this `experimental`? What's experimental about it?

That's a question that remains to be answered. If this grammar doesn't get used much, it will probably be experimental
forever. If there's interest in it, hopefully others are willing to help define what it would take to make this not
"experimental". Reaching a point where we can offer some stability guarantees is probably a good first step.

### What stability guarantees do you offer?

Currently none. It seems prudent that someday, the grammar would be versioned and any removal or changes to nodes would
be considered a breaking change, and accompanied by a major version bump. Right now, this is not the place to go for
stability unless you pin an exact commit hash.

### Where is your `parser.c`?

This repository currently omits most of the code that is autogenerated during a build. This means, for instance, that
`grammar.json` and `parser.c` are both only available following a build. It also significantly reduces noise during
diffs.

The side benefit of not checking in `parser.c` is that parsers aren't always backwards compatible. If you need a parser,
generate it yourself using the CLI; all the information to do so is available in this package. By doing that, you'll
also know for sure that your parser version and your library version are compatible.
