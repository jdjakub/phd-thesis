ClassicThesis.pdf: Chapters/ch-intro.tex Chapters/ch-background.tex OwnPublications.bib ../thesis.bib
	lualatex ClassicThesis
	biber ClassicThesis
	lualatex ClassicThesis
	lualatex ClassicThesis
	
Chapters/%.tex: ../../%.md
	pandoc $< --output $@
	
