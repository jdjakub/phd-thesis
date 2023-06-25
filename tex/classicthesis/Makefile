ClassicThesis.pdf: ../abstract.tex FrontBackmatter/Abstract.tex ../acks.tex FrontBackmatter/Acknowledgments.tex Chapters/ch-intro.tex Chapters/ch-background.tex ../fig/format-errors.tex Chapters/ch-analysis.tex ../fig/plaf-substr-prod.tex ../fig/change-by-re-creation.tex ../fig/user-impl.tex Chapters/ch-tech-dims.tex Chapters/ch-bl.tex Chapters/ch-conclusion.tex ClassicThesis.tex classicthesis-config.tex OwnPublications.bib ../thesis.bib
	lualatex ClassicThesis
	biber ClassicThesis
	lualatex ClassicThesis
	lualatex ClassicThesis
	
Chapters/%.tex: ../../%.md
	pandoc $< --output $@
	
../%.tex: ../../%.md
	pandoc $< --output $@
	
