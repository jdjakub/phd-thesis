thesis.pdf: *.tex
	lualatex thesis
	biber thesis
	lualatex thesis
	lualatex thesis
	
%.tex: ../%.md
	pandoc $< --output $@
	
