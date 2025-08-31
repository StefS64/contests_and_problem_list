LATEX      = pdflatex
LATEXFLAGS = -interaction=nonstopmode -halt-on-error -output-directory=../build


CONTESTS = $(shell find ../contests -type f -name '*.tex' | sort)

CONTEST_PDFS = $(patsubst ../contests/%.tex,../bin/%.pdf,$(CONTESTS))

PROBLEM_TXS = $(shell find ../problems -type f -name '*.tex' | sort)

PROBLEM_PDFS = $(patsubst ../problems/%.tex,../bin/problems/%.pdf,$(PROBLEM_TXS))

WRAPPERS = $(patsubst ../problems/%.tex,wrapped/%.tex,$(PROBLEM_TXS))

# Function: extract dependencies (\includeProblem{...})
# Produces a list of ../problems/<path>.tex (adds .tex if missing)
define deps
$(shell grep -o '\\includeProblem{[^}]*}' $1 | \
       sed -E 's/\\includeProblem\{([^}]*)\}/\1/' | \
       sed -E 's|^|../problems/|' | sed -E '/\.tex$$/! s|$$|.tex|')
endef


all: $(CONTEST_PDFS) $(PROBLEM_PDFS)

# generate a usage map file (plain text) listing which contests include which problems
usage-map: build/problem-usage.txt

build/problem-usage.txt: $(PROBLEM_TXS)
	@echo "Generating problem usage map..."
	@echo "Problem -> Contests" > $@
	@for p in $(PROBLEM_TXS); do \
	  rel=$$(echo $$p | sed 's|^\.\./problems/||'); \
	  rel_noext=$$(echo $$rel | sed 's|\.tex$$||'); \
	  base=$$(basename "$$rel_noext"); \
	  echo -n "$$rel:" >> $@; \
	  usages=$$( (grep -rlF "\\includeProblem{$$rel}" ../contests || true; \
                  grep -rlF "\\includeProblem{$$rel_noext}" ../contests || true; \
                  grep -rlE "\\\\includeProblem\\{([^}]*/)?$$base(\\.tex)?\\}" ../contests || true) | sort -u ); \
	  if [ -z "$$usages" ]; then echo " (not used)" >> $@; \
	  else echo >> $@; \
	       for u in $$usages; do echo "  - $$u" >> $@; done; \
	  fi; \
	done

# Rule: generate wrapper .tex that includes the problem and a "Used in" itemize
# wrapped/geo/problem1.tex <- ../problems/geo/problem1.tex
wrapped/%.tex: ../problems/%.tex
	@echo "Generating wrapper for problem $*"
	@mkdir -p $(dir $@)
	@echo '% Auto-generated wrapper' > $@
	@printf '\\documentclass{article}\n\\usepackage[utf8]{inputenc}\n\\input{../utils/helpers.tex}\n\\begin{document}\n' >> $@
	@printf '\\input{../problems/%s.tex}\n\n' "$*" >> $@
	@printf '\\section*{Used in}\n\\begin{itemize}\n' >> $@
	@rel="$*"; \
	base=$$(basename "$$rel" .tex); \
	cnts=$$( { \
      grep -rlF "\\\\includeProblem{$$rel}.tex" ../contests || true; \
      grep -rlF "\\\\includeProblem{$$rel}" ../contests || true; \
      grep -rlE "\\\\includeProblem\\{([^}]*/)?$$base(\\.tex)?\\}" ../contests || true; \
    } | sort -u ); \
	if [ -z "$$cnts" ]; then \
	  printf '  \\item (not used in any contest)\n' >> $@; \
	else \
	  for c in $$cnts; do \
	    printf '  \\item %s\n' "$$c" >> $@; \
	  done; \
	fi
	@printf '\\end{itemize}\n\\end{document}\n' >> $@

# Build standalone PDF for each wrapper and move to ../bin/problems/.../name.pdf
# Use jobname based on wrapper path with slashes -> underscores to avoid clashes
../bin/problems/%.pdf: wrapped/%.tex
	@echo "Building standalone PDF for problem $*"
	@mkdir -p $(dir $@)
	@name=$$(echo "$<" | sed 's|^wrapped/||; s|/|_|g; s|\.tex$$||'); \
	$(LATEX) -jobname="$$name" $(LATEXFLAGS) $<; \
	mv -f ../build/$$name.pdf $@

# allow building all problem PDFs
problems: $(PROBLEM_PDFS)

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
	@mv -f ../build/$(basename $(notdir $(2))).pdf $(1)
endef

$(foreach c,$(CONTESTS),$(eval $(call make_rule,$(patsubst ../contests/%.tex,../bin/%.pdf,$(c)),$(c))))

mirror-structure:
	@find ../contests -type d -print | sed 's|^\.\./contests|..\/bin|' | xargs -r -I{} mkdir -p {}

clean:
	@echo "Removing build auxiliaries and clearing ../bin/ ..."
	@rm -f ../build/*.aux ../build/*.log ../build/*.toc ../build/*.out ../build/*.pdf
	@rm -f $(CONTEST_PDFS) $(PROBLEM_PDFS)
	@rm -rf wrapped/ build/problem-usage.txt
	@mkdir -p ../bin
	@rm -rf ../bin/* ../bin/.[!.]* ../bin/..?* || true

clean-pdf:
	rm -f ../build/*.aux ../build/*.log ../build/*.toc ../build/*.out ../build/*.fls ../build/*.fdb_latexmk ../build/*.synctex.gz
	@rm -rf wrapped/ build/problem-usage.txt
	@echo "Cleaned auxiliary files; PDFs preserved."

.PHONY: all clean problems usage-map clean-pdf