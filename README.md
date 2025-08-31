# Contest repository — quick guide

This repository stores problems as separate LaTeX fragments and assembles contests from them. The build system (build/Makefile) can:

- produce contest PDFs from files in `contests/` (each contest `.tex` uses `\includeProblem{...}`),
- generate standalone per-problem PDFs (with a "Used in" list),
- produce a plain usage map `build/problem-usage.txt`.

Directory overview
- `problems/` — problem fragments (can live in subfolders: `kombi/`, `geo/`, ...).
- `contests/` — contest `.tex` files that call `\includeProblem{...}`.
- `utils/helpers.tex` — helper macros, e.g. `\includeProblem{path}` and `\problemTitle{...}`.
- `build/Makefile` — build rules (run from `build/` or via `make -C build`).
- `bin/` — generated PDFs (contests -> `bin/<contest-path>`, problems -> `bin/problems/<path>`).

How problems are included
- In contest files use `\includeProblem{relative/path/to/problem.tex}` where the path is relative to `problems/`.
  Example in `contests/example/example_contest.tex`:
  `\includeProblem{dummy/problem1.tex}`

- `utils/helpers.tex` defines `\includeProblem` to `\input{../problems/#1}` so contest compilation pulls the problem body into the contest document.


## Compiling a single problem PDF

To build a standalone PDF for a specific problem (e.g. `problems/dummy/problem1.tex`), run:

```bash
make -C build ../bin/problems/dummy/problem1.pdf
```

This will:
- Generate a wrapper `.tex` file for the problem in `build/wrapped/dummy/problem1.tex`.
- Compile it to PDF, placing the result in `bin/problems/dummy/problem1.pdf`.

You can also use tab completion to find available problem PDFs, or run `make -C build problems` to build all of them.

---
Per-problem standalone PDF generation
- The Makefile automatically creates a wrapper (.tex) that:
  - loads `utils/helpers.tex` so problem macros (`\problemTitle`, `\problemStatement`, ...) work,
  - `\input` the problem file,
  - appends a "Used in" itemize listing contests that include that problem (Makefile finds matches by several grep patterns).
- The wrapper is compiled (with a unique jobname) and the PDF placed into `bin/problems/<path>.pdf`.

Example problem file (good template)
```tex
// filepath: [dummy.tex](http://_vscodecontentref_/0)
\problemTitle{Choosing Teams}

\problemStatement{
A school has 12 students who want to play football. The coach wants to form a team of 5 players.
}

\begin{enumerate}
  \item How many different teams of 5 players can be formed?
  \item If two students, Alice and Bob, refuse to play together on the same team, how many different teams are possible?
\end{enumerate}
```