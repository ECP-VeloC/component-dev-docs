all: components.pdf components.png

components.pdf: components.gv
	dot -Tpdf $< -o $@

components.png: components.gv
	dot -Tpng $< -o $@

clean:
	rm components.pdf
	rm components.png
