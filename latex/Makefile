# latex/Makefile
.PHONY: all clean watch

all: main.pdf

main.pdf: main.tex chapters/*.tex references.bib
	pdflatex main.tex
	bibtex main 2>/dev/null || true
	pdflatex main.tex
	pdflatex main.tex

clean:
	rm -f *.aux *.log *.toc *.bbl *.blg *.out *.pdf chapters/*.aux

watch:
	latexmk -pdf -pvc main.tex