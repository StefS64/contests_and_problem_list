LATEX      = pdflatex
LATEXFLAGS = -interaction=nonstopmode -halt-on-error -output-directory=../build

# All contest TeX files
CONTESTS = $(wildcard ../contests/*.tex)
# PDF targets (placed in ../bin)
CONTEST_PDFS = $(CONTESTS:../contests/%.tex=../bin/%.pdf)

PROBLEMS =  ../problems/*

# Function: extract dependencies (\includeProblem{...})
define deps
$(shell grep -o '\\includeProblem{[^}]*}' $1 | \
       sed -E 's/\\includeProblem\{([^}]*)\}/..\/problems\/\1/')
endef

# Default target: build all contest PDFs
all: $(CONTEST_PDFS)

$(PROBLEMS):
	@echo "Read $@"


# Generate one rule per contest: ../bin/<name>.pdf depends on the .tex and its included problems
define make_rule
$(1): $(2) $(call deps,$(2))
	@echo "Dependencies for $(2):"
	@echo "$(call deps,$(2))"
	@echo "Raw dependencies:"
	@grep -o '\\includeProblem{[^}]*}' $(2) | sed -E 's/\\includeProblem\{([^}]*)\}/..\/problems\/\1/'
	@mkdir -p ../build ../bin
	$(LATEX) $(LATEXFLAGS) $(2)
	$(LATEX) $(LATEXFLAGS) $(2)
	@mv -f ../build/$(basename $(notdir $(2))).pdf ../bin/$(basename $(notdir $(2))).pdf
endef

$(foreach c,$(CONTESTS),$(eval $(call make_rule,$(c:../contests/%.tex=../bin/%.pdf),$(c))))

# Clean up auxiliary files (aux/log/toc/out in ../build, only PDFs in ../bin)
clean:
	rm -f ../build/*.aux ../build/*.log ../build/*.toc ../build/*.out ../build/*.pdf
	rm -f ../bin/*.pdf

# Phony targets
.PHONY: all clean