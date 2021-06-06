.PHONY: strace-docker curl-docker

strace-docker:
	docker build . -t strace-docker
	docker run strace-docker

curl-docker:
	docker build . -f Dockerfile-curl -t curl-docker
	docker run curl-docker
