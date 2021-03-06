SHELL=/bin/bash

test:
	python -m spacy download en
	py.test
	DATA_PATH=. PATH="$(CURDIR):$(PATH)" ENTRYPOINT=$(CURDIR)/entrypoint.sh bats test.bats

IMAGE_NAME=starts-with-verb

GITHUB_WORKSPACE=/github/workspace

local-integration-test:
	docker build -t $(IMAGE_NAME) .
	GITHUB_EVENT_PATH=$$PWD/event.json; \
	trap 'rm $$GITHUB_EVENT_PATH' EXIT; \
	echo '{"pull_request": {"title": "start with a verb"}}' > $$GITHUB_EVENT_PATH; \
	result=$$(docker run -v $(GITHUB_WORKSPACE) -w $(GITHUB_WORKSPACE) -v $$GITHUB_EVENT_PATH:/tmp/event.json -e GITHUB_WORKSPACE=$(GITHUB_WORKSPACE) \
		      -e GITHUB_EVENT_PATH=/tmp/event.json $(IMAGE_NAME)); \
	[[ $$result = "First word 'starts' is a recognized verb" ]] || echo "Unexpected output: $$result"

DOCKER_COMPOSE=docker-compose -f docker-compose.test.yml

remote-integration-test:
	$(DOCKER_COMPOSE) build sut
	result=$$($(DOCKER_COMPOSE) run sut) && [[ $$result = "First word 'starts' is a recognized verb" ]] || echo "Unexpected output: $$result"

.PHONY: test
