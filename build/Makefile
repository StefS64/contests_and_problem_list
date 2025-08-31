LATEX      = pdflatex
LATEXFLAGS = -interaction=nonstopmode -halt-on-error -output-directory=../build

# Main contest TeX file
CONTEST = ../contests/contest1.tex
PROBLEMS =  ../problems/*



# Output PDF name
CONTEST_PDF = $(CONTEST:../contests/%.tex=%.pdf)

# Function: extract dependencies (\includeProblem{...})
define deps
$(shell grep -o '\\includeProblem{[^}]*}' $1 | \
       sed -E 's/\\includeProblem\{([^}]*)\}/..\/problems\/\1/')
endef

# Default target
all: $(CONTEST_PDF)

$(PROBLEMS):
	@echo "Read $@"


# Rule: build the main contest PDF
%.pdf: ../contests  $(call deps,$(CONTEST))
	@echo "Dependencies for $(CONTEST):"
	@echo "$(call deps,$(CONTEST))"
	@echo "Raw dependencies:"
	@grep -o '\\includeProblem{[^}]*}' $(CONTEST) | sed -E 's/\\includeProblem\{([^}]*)\}/..\/problems\/\1/'
	$(LATEX) $(LATEXFLAGS) $<

# Clean up auxiliary files
clean:
	rm -f ../build/*.aux ../build/*.log ../build/*.toc ../build/*.out ../build/*.pdf

# Phony targets
.PHONY: all clean