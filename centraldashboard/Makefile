IMG ?= centraldashboard
TAG ?= v1.9.0-rc.0#$(shell git describe --tags --always)
COMMIT = $(shell git rev-parse HEAD)
DOCKERFILE ?= Dockerfile
ARCH ?= linux/amd64


all: build

clean:
	rm -rf coverage/ dist/ node_modules/ .nyc_output/

# Builds the package locally
build-local:
	npm install

# Runs unit tests with coverage
test: build-local
	npm run coverage

# To build without the cache set the environment variable
# export DOCKER_BUILD_OPTS=--no-cache
docker-build:
	docker build ${DOCKER_BUILD_OPTS} -t farshadg99/$(IMG):$(TAG) . \
	  --build-arg kubeflowversion=$(shell git describe --abbrev=0 --tags) \
	  --build-arg commit=$(COMMIT) \
      --label=git-version=$(TAG)
	@echo Built $(IMG):$(TAG)

docker-push:
	docker push farshadg99/$(IMG):$(TAG)

.PHONY: docker-build-multi-arch
docker-build-multi-arch: ##  Build multi-arch docker images with docker buildx
	docker buildx build --load --platform ${ARCH} --tag ${IMG}:${TAG} -f ${DOCKERFILE} . --build-arg kubeflowversion=$(shell git describe --abbrev=0 --tags) --build-arg commit=$(COMMIT) --label=git-version=$(TAG)


.PHONY: docker-build-push-multi-arch
docker-build-push-multi-arch: ## Build multi-arch docker images with docker buildx and push to docker registry
	docker buildx build --platform ${ARCH} --tag ${IMG}:${TAG} --push -f ${DOCKERFILE} . --build-arg kubeflowversion=$(shell git describe --abbrev=0 --tags) --build-arg commit=$(COMMIT) --label=git-version=$(TAG)

# Build but don't attach the latest tag. This allows manual testing/inspection of the image
# first.
push: build
	gcloud docker -- push $(IMG):$(TAG)
	@echo Pushed $(IMG) with  :$(TAG) tags

