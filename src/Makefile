build-img:
	docker build -t fc-wordpress-custom-container  -f Dockerfile ./

deploy:
	s deploy

sync-nas:
	s nas sync ./conf

.PHONY: deploy build-img sync-nas