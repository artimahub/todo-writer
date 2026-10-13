# todo-writer

Finds public declarations that have no Scaladoc and marks each one in place, so a coding agent can
be pointed at a file and told to fill the markers in rather than told to go hunting.

Written for the Scala 3 Scaladoc work funded by the Sovereign Tech Agency through the Scala Center,
and used here in the *Supercharge your coding with AI and Scala 3* workshop.

## What you need

A JDK 17 or newer and sbt — the same toolchain as the rest of the workshop. No other dependencies.

```sh
git clone https://github.com/artimahub/todo-writer.git
cd todo-writer
sbt test        # 278 tests, if you want to check it works
```

## Using it

Point it at a source directory. Dry run first, which reports and changes nothing:

```sh
sbt "run /path/to/geny/geny/src --only-undocumented --dry"
```

```
Summary:
  Methods: 105, with issues: 105 (100.0%)
  Classes: 8, with issues: 8 (100.0%)
  Traits: 2, with issues: 2 (100.0%)

Scanned 5 files. Found 259 issues.
```

Then for real, which edits the files in place:

```sh
sbt "run /path/to/geny/geny/src --only-undocumented"
```

It writes a marker immediately above each undocumented declaration:

```scala
  /** TODO FILL IN
   *
   *  @param other TODO FILL IN
   */
  override def equals(other: Any) = other match {
```

## Things worth knowing

- **Don't commit the markers.** They are scaffolding. You run this locally, your pipeline replaces
  each marker with a real doc comment, and by the time you have something to commit the markers are
  gone — because replacing them *is* the work.
- **Safe to re-run.** A second run over an already-marked tree finds nothing, so it will not
  double-mark. And if a step goes wrong and you revert it, re-running puts the markers back.
- **It leaves other TODOs alone.** The marker is `TODO FILL IN`, which does not collide with
  ordinary `// TODO` comments already in a codebase.
- **`--only-undocumented` is the flag you want here**, for declarations with no Scaladoc at all.
  Without it, the tool also fills in missing `@param`, `@return` and `@tparam` tags on comments that
  already exist.

## Other options

```
Usage: todo-writer <folder> [options]

  --dry                 Report only; do not write changes to files
  --only-undocumented   Only declarations that have no Scaladoc block at all
  --skip-undocumented   The opposite: leave those alone
  --skip-todo           Do not insert TODO placeholders for missing tags
  --migrate-markdown    Migrate Wikidoc-style scaladoc to Markdown
  --list-broken-link    Check Scaladoc for broken HTTP/HTTPS links
  --json                Output results as JSON
  --help                Full list
```

Pull requests welcome — fork it if you want to send one.
