# Contest Repository

This repository is designed for creating and managing programming contests. It organizes problems into separate LaTeX files and allows for easy inclusion of these problems in contest files, which can then be compiled into PDFs.

## Project Structure

- **problems/**: Contains individual problem files.
  - `problem1.tex`: LaTeX source for problem 1.
  - `problem2.tex`: LaTeX source for problem 2.
  - `problem3.tex`: LaTeX source for problem 3.

- **contests/**: Contains contest files that reference the problem files.
  - `contest1.tex`: LaTeX source for contest 1.
  - `contest2.tex`: LaTeX source for contest 2.

- **utils/**: Contains utility files for flags and helpers.
  - `helpers.tex`: Contains helper functions and macros for formatting.

- **build/**: Contains build instructions.
  - `Makefile`: Instructions for compiling the LaTeX files into PDFs.

- **.gitignore**: Specifies files and directories to ignore in version control.

## Adding New Problems

To add a new problem:
1. Create a new `.tex` file in the `problems/` directory.
2. Follow the structure of existing problem files, including the problem statement, constraints, and examples.

## Adding New Contests

To create a new contest:
1. Create a new `.tex` file in the `contests/` directory.
2. Include the relevant problem files using the appropriate LaTeX commands.

## Compiling PDFs

To compile the contest files into PDFs, use the provided `Makefile` in the `build/` directory. Run the following command in your terminal:

```
make
```

This will generate PDF files for each contest in the `contests/` directory.

## License

This project is licensed under the MIT License. See the LICENSE file for more details.