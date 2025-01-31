help:           ## Show this help.
	@fgrep -h "##" $(MAKEFILE_LIST) | fgrep -v fgrep | sed -e "s/\\$$//" | sed -e "s/##//"

IMG_DIR = docs/images
FIND=find
# use env var PRESENTAION_NAME=presentation
NAME=KW05Neufeld
QMD=index.qmd

DOCS_PATH=docs/
DOCS_TAR_PATH=$(NAME).tar.gz
REMOTE_PATH=~/
AWS_PREFIX=/usr/share/nginx/
LOC_PREFIX=/usr/share/nginx/
INTERAKTIV=html/interaktiv/
SERVER = aws-server

# Convert paths to the correct format for Windows
ifeq ($(OS), Windows_NT)
    # IMG_DIR := $(subst /,\\,$(IMG_DIR))
	FIND=gfind
endif

upload-js:
	cd ../interaktiv-frontend && make upload

render:         ## Render the markdown with quarto into docs/
	@quarto render $(QMD)
	@cp -rf images/icons docs/images/

pdf:            ## Render the markdown with quarto into a pdf
	@quarto render $(QMD) --to pdf

convert:		## Convert the png and jpq to webp (TODO)
	find images -type f \( -iname "*.png" -o -iname "*.jpg" \) -exec sh -c 'cwebp "$$1" -q 80 -o "$${1%.*}.webp"' _ {} \;

serve:          ## Serves the project via quarto
serve: render
	quarto preview

images:         ## Copy images to the docs folder
	@cp -rf images/icons docs/images/icons

upload: render  ## Upload the docs to the server
	cp -rf images/icons docs/images/icons
	tar -cvzf $(DOCS_TAR_PATH) $(DOCS_PATH) && \
	scp -r $(DOCS_TAR_PATH) $(SERVER):$(REMOTE_PATH) && \
	ssh $(SERVER) "rm -rf $(AWS_PREFIX)$(INTERAKTIV)$(DOCS_PATH)" && \
	ssh $(SERVER) "tar -xvf $(REMOTE_PATH)$(DOCS_TAR_PATH) -C $(AWS_PREFIX)$(INTERAKTIV)" && \
	cd ../../interaktiv-frontend/ && make build && make upload

load: render    ## load docs to local nginx server
	cp -r $(DOCS_PATH)/* $(LOC_PREFIX)$(INTERAKTIV)$(DOCS_PATH)
	cd ../../interaktiv-frontend/ && make build_local && make load

dev:			## Serves the project in development mode
	cd docs && python -m http.server 5050

clean:          ## clean up
	rm -rf docs
	rm -rf .quarto
	rm -rf node_modules
	gfind . -type f -name '*~' -delete


