TEX_FILES = ../abstract.tex FrontBackmatter/Abstract.tex ../acks.tex FrontBackmatter/Acknowledgments.tex Chapters/ch-intro.tex Chapters/ch-background.tex ../fig/format-errors.tex Chapters/ch-analysis.tex ../fig/plaf-substr-prod.tex ../fig/change-by-re-creation.tex ../fig/user-impl.tex Chapters/ch-tech-dims.tex Chapters/ch-bl.tex Chapters/ch-related-work.tex Chapters/ch-conclusion.tex Chapters/appendix-bl.tex Chapters/appendix-all-dims.tex ../all-tech-dims.tex ClassicThesis.tex classicthesis-config.tex

ClassicThesis.pdf: $(TEX_FILES) OwnPublications.bib ../thesis.bib
	lualatex ClassicThesis
	biber ClassicThesis
	lualatex ClassicThesis
	lualatex ClassicThesis
	
Chapters/%.tex: ../../%.md
	pandoc $< --output $@

../%.tex: ../../%.md
	pandoc $< --output $@
	
diff.pdf: diff.tex
	lualatex diff
	biber diff
	lualatex diff
	lualatex diff

expanded-new.tex: $(TEX_FILES)
	latexpand ClassicThesis.tex > $@
	
diff.tex: expanded-new.tex
	latexdiff expanded-old.tex $< > $@