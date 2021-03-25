all: components.pdf components.png components.svg

components.pdf: components.gv
	dot -Tpdf $< -o $@

components.png: components.gv
	dot -Tpng $< -o $@

components.svg: components.gv
	dot -Tsvg $< -o $@

clean:
	rm components.pdf
	rm components.png
	rm components.svg
