TEST?=$$(go list ./... |grep -v 'vendor')
GOFMT_FILES?=$$(find . -name '*.go' |grep -v vendor)
WEBSITE_REPO=github.com/hashicorp/terraform-website

MODULE   = $(shell env GO111MODULE=on $(GO) list -m)
VERSION ?= $(shell git describe --tags --always --dirty --match=v* 2> /dev/null || \
			cat $(CURDIR)/.version 2> /dev/null || echo v0)

PROVIDER_HOSTNAME=registry.upcloud.com
PROVIDER_NAMESPACE=upcloud
PROVIDER_TYPE=upcloud
PROVIDER_TARGET=$(shell go env GOOS)_$(shell go env GOARCH)
PROVIDER_PATH=~/.terraform.d/plugins/$(PROVIDER_HOSTNAME)/$(PROVIDER_NAMESPACE)/$(PROVIDER_TYPE)/$(VERSION)/$(PROVIDER_TARGET)

default: build

build: fmtcheck
	@mkdir -p $(PROVIDER_PATH)
	go build \
		-tags release \
		-ldflags '-X $(MODULE)/internal/globals.Version=$(VERSION)' \
		-o $(PROVIDER_PATH)/terraform-provider-$(PROVIDER_NAMESPACE)_v$(VERSION)

build_0_12: fmtcheck
	go install

test: fmtcheck
	go test $(TEST) || exit 1
	echo $(TEST) | \
		xargs -t -n4 go test $(TESTARGS) -timeout=300s -parallel=4 -count=1

testacc: fmtcheck
	TF_ACC=1 go test $(TEST) -v $(TESTARGS) -timeout 240m

vet:
	@echo "go vet ."
	@go vet $$(go list ./... | grep -v vendor/) ; if [ $$? -ge 1 ]; then \
		echo ""; \
		echo "Vet found suspicious constructs. Please check the reported constructs"; \
		echo "and fix them if necessary before submitting the code for review."; \
		exit 1; \
	fi

fmt:
	gofmt -w $(GOFMT_FILES)

fmtcheck:
	@sh -c "'$(CURDIR)/scripts/gofmtcheck.sh'"

errcheck:
	@sh -c "'$(CURDIR)/scripts/errcheck.sh'"

test-compile:
	@if [ "$(TEST)" = "./..." ]; then \
		echo "ERROR: Set TEST to a specific package. For example,"; \
		echo "  make test-compile TEST=./aws"; \
		exit 1; \
	fi
	go test -c $(TEST) $(TESTARGS)

update-deps:
	go mod vendor

.PHONY: build test testacc vet fmt fmtcheck errcheck test-compile update-deps website website-test build_0_13



website:
ifeq (,$(wildcard $(GOPATH)/src/$(WEBSITE_REPO)))
	echo "$(WEBSITE_REPO) not found in your GOPATH (necessary for layouts and assets), getting..."
	git clone https://$(WEBSITE_REPO) $(GOPATH)/src/$(WEBSITE_REPO)
endif
	ln -s ../../../ext/providers/$(PROVIDER_TYPE)/website/$(PROVIDER_TYPE).erb $(GOPATH)/src/$(WEBSITE_REPO)/content/source/layouts/$(PROVIDER_TYPE).erb || true
	ln -s ../../../../ext/providers/$(PROVIDER_TYPE)/website/docs $(GOPATH)/src/$(WEBSITE_REPO)/content/source/docs/providers/$(PROVIDER_TYPE) || true
	@$(MAKE) -C $(GOPATH)/src/$(WEBSITE_REPO) website-provider PROVIDER_PATH=$(shell pwd) PROVIDER_NAME=$(PROVIDER_TYPE)

website-test:
ifeq (,$(wildcard $(GOPATH)/src/$(WEBSITE_REPO)))
	echo "$(WEBSITE_REPO) not found in your GOPATH (necessary for layouts and assets), getting..."
	git clone https://$(WEBSITE_REPO) $(GOPATH)/src/$(WEBSITE_REPO)
endif
	ln -s ../../../ext/providers/$(PROVIDER_TYPE)/website/$(PROVIDER_TYPE).erb $(GOPATH)/src/$(WEBSITE_REPO)/content/source/layouts/$(PROVIDER_TYPE).erb || true
	ln -s ../../../../ext/providers/$(PROVIDER_TYPE)/website/docs $(GOPATH)/src/$(WEBSITE_REPO)/content/source/docs/providers/$(PROVIDER_TYPE) || true
	@$(MAKE) -C $(GOPATH)/src/$(WEBSITE_REPO) website-provider-test PROVIDER_PATH=$(shell pwd) PROVIDER_NAME=$(PROVIDER_TYPE)

