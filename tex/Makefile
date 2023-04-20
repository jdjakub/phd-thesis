chapter.pdf: *.tex
	lualatex chapter
	biber chapter
	lualatex chapter
	lualatex chapter

thesis.pdf: *.tex thesis.bib
	lualatex thesis
	biber thesis
	lualatex thesis
	lualatex thesis
	
%.tex: ../%.md
	pandoc $< --output $@
	
