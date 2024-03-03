TIKZ_FILES = ../fig/format-errors.tex ../fig/plaf-substr-prod.tex ../fig/change-by-re-creation.tex ../fig/user-impl.tex ../fig/bl-layers.tex
# Screw LaTeX. Might as well use drw[1]. My Mathcha diagrams never end up looking like they do in Mathcha. I could spend 1 billion years manually poking coordinates in a text file, or I can just use PNGs. Once again, I am defeated.
# [1] https://programmingmadecomplicated.wordpress.com/2017/10/29/introducing-drw-a-useful-tool-to-solve-a-practical-problem/
IMG_FILES = ../fig/semantics/*.png
APPENDICES = Chapters/appendix-bl.tex Chapters/appendix-all-dims.tex Chapters/appendix-substrate-ref.tex Chapters/appendix-masp-ref.tex ../all-tech-dims.tex Chapters/appendix-bl-steps.tex
TEX_FILES = ../abstract.tex FrontBackmatter/Abstract.tex ../acks.tex FrontBackmatter/Acknowledgments.tex Chapters/ch-intro.tex Chapters/ch-background.tex  Chapters/ch-analysis.tex Chapters/ch-tech-dims.tex Chapters/ch-bl.tex Chapters/ch-related-work.tex Chapters/ch-conclusion.tex  ClassicThesis.tex classicthesis-config.tex $(TIKZ_FILES) $(IMG_FILES) $(APPENDICES)

ClassicThesis.pdf: $(TEX_FILES) OwnPublications.bib ../thesis.bib
ifndef RESUME
	lualatex ClassicThesis
endif
ifdef FULL
	biber ClassicThesis
	lualatex ClassicThesis
	lualatex ClassicThesis
endif
	
Chapters/%.tex: ../../%.md
	pandoc $< --output $@

../%.tex: ../../%.md
	pandoc $< --output $@
	
diff.pdf: diff.tex
	lualatex diff
	biber diff
	lualatex diff
	lualatex diff

diff-bl.pdf: diff-bl.tex
	lualatex diff-bl
	biber diff-bl
	lualatex diff-bl
	lualatex diff-bl

expanded-new.tex: $(TEX_FILES)
	latexpand ClassicThesis.tex > $@
	
diff.tex: expanded-new.tex
	latexdiff expanded-old.tex $< > $@

diff-bl.tex: expanded-new.tex
	latexdiff expanded-old-bl-only.tex $< > $@