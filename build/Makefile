LATEX      = pdflatex
LATEXFLAGS = -interaction=nonstopmode -halt-on-error -output-directory=../build

# find all .tex files under ../contests (keeps subdirs)
CONTESTS = $(shell find ../contests -type f -name '*.tex' | sort)
# map each ../contests/.../name.tex -> ../bin/.../name.pdf (preserves dir tree)
CONTEST_PDFS = $(patsubst ../contests/%.tex,../bin/%.pdf,$(CONTESTS))

PROBLEMS =  ../problems/*

# Function: extract dependencies (\includeProblem{...})
define deps
$(shell grep -o '\\includeProblem{[^}]*}' $1 | \
       sed -E 's/\\includeProblem\{([^}]*)\}/..\/problems\/\1/')
endef

# --------- NEW: per-problem standalone build and usage map ---------
# list of problem tex sources
PROBLEM_TXS = $(wildcard ../problems/*.tex)
# map each ../problems/name.tex -> ../bin/problems/name.pdf
PROBLEM_PDFS = $(patsubst ../problems/%.tex,../bin/problems/%.pdf,$(PROBLEM_TXS))
# wrappers placed in the current build dir (this Makefile is run from build/)
WRAPPERS = $(patsubst ../problems/%.tex,wrapped-%.tex,$(PROBLEM_TXS))

# default target now also builds problem PDFs (optional: keep only contests if desired)
all: $(CONTEST_PDFS) $(PROBLEM_PDFS)

# generate a usage map file (plain text) listing which contests include which problems
usage-map: build/problem-usage.txt

build/problem-usage.txt: $(PROBLEM_TXS)
	@echo "Generating problem usage map..."
	@echo "Problem -> Contests" > $@
	@for p in $(PROBLEM_TXS); do \
	  name=$$(basename $$p); \
	  echo -n "$$name:" >> $@; \
	  usages=$$(grep -rl "\\\\includeProblem{$$name}" ../contests || true); \
	  if [ -z "$$usages" ]; then echo " (not used)" >> $@; \
	  else echo >> $@; \
		   for u in $$usages; do echo "  - $$u" >> $@; done; \
	  fi; \
	done

# Rule: generate wrapper .tex that includes the problem and a "Used in" itemize
wrapped-%.tex: ../problems/%.tex
	@echo "Generating wrapper for problem $*"
	@printf "%% Auto-generated wrapper for problem %s\n" "$*" > $@
	@printf '%s\n' '\documentclass{article}' '\usepackage[utf8]{inputenc}' '\usepackage{amsmath}' '\usepackage{amsfonts}' '\begin{document}' '% problem body' "\\input{../problems/$*.tex}" '' '\section*{Used in}' '\begin{itemize}' >> $@
	@cnts=$$(grep -rl "\\\\includeProblem{$*.tex}" ../contests || true); \
	if [ -z "$$cnts" ]; then \
	  printf '%s\n' '\item (not used in any contest)' >> $@; \
	else \
	  for c in $$cnts; do \
	    printf '  \\item %s\n' "$$c" >> $@; \
	  done; \
	fi
	@printf '%s\n' '\end{itemize}' '\end{document}' >> $@

# Build standalone PDF for each wrapper and move to ../bin/problems/name.pdf
../bin/problems/%.pdf: wrapped-%.tex
	@echo "Building standalone PDF for problem $*"
	@mkdir -p ../bin/problems
	$(LATEX) $(LATEXFLAGS) $<
	@mv -f ../build/$(basename $(notdir $<)).pdf $@

# allow building all problem PDFs
problems: $(PROBLEM_PDFS)

# Default target: build all contest PDFs
all: $(CONTEST_PDFS)

$(PROBLEMS):
	@echo "Read $@"


# Generate one rule per contest: ../bin/.../<name>.pdf depends on the .tex and its included problems
define make_rule
$(1): $(2) $(call deps,$(2))
	@echo "Dependencies for $(2):"
	@echo "$(call deps,$(2))"
	@echo "Raw dependencies:"
	@grep -o '\\includeProblem{[^}]*}' $(2) | sed -E 's/\\includeProblem\{([^}]*)\}/..\/problems\/\1/'
	@mkdir -p ../build $(dir $(1))
	$(LATEX) $(LATEXFLAGS) $(2)
	$(LATEX) $(LATEXFLAGS) $(2)
	@mv -f ../build/$(basename $(notdir $(2))).pdf $(1)
endef

$(foreach c,$(CONTESTS),$(eval $(call make_rule,$(patsubst ../contests/%.tex,../bin/%.pdf,$(c)),$(c))))

# Mirror contests directory structure into ../bin (creates corresponding dirs)
mirror-structure:
	@find ../contests -type d -print | sed 's|^\.\./contests|..\/bin|' | xargs -r -I{} mkdir -p {}

# Clean up auxiliary files (aux/log/toc/out in ../build, PDFs in ../bin corresponding to contests)
clean:
	rm -f ../build/*.aux ../build/*.log ../build/*.toc ../build/*.out ../build/*.pdf
	@rm -f $(CONTEST_PDFS) $(PROBLEM_PDFS) wrapped-*.tex build/problem-usage.txt

# Phony targets
.PHONY: all clean problems usage-map