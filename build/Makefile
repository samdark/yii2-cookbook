

APIDOC=vendor/bin/apidoc

book.pdf: ../book/* ../book/images/* title.tex
	$(APIDOC) guide --template=pdf --interactive=0 ../book/ ./pdf
	sed -i 's/\\begin{document}/\\usepackage{tikz}\\begin{document}/' ./pdf/main.tex
	sed -i 's/\\newcommand{\\plainTitle}{[^}]*}/\\newcommand{\\plainTitle}{Yii 2.0 Cookbook}/' ./pdf/main.tex
	sed -i 's/\\newcommand{\\plainAuthors}{/\\newcommand{\\plainAuthors}{Yii community and Alexander Makarov}\\newcommand{\\yiiAuthors}{/' ./pdf/main.tex
	cp title.tex ./pdf/title.tex
	cd pdf && make
	cp ./pdf/guide.pdf book.pdf

.PHONY: clean

clean:
	-rm -rf ./pdf book.pdf
