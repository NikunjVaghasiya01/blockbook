SHELL = /bin/bash
VERSION ?= devel
GITCOMMIT = $(shell cd /src && git describe --always --dirty)
BUILDTIME = $(shell date --iso-8601=seconds)
LDFLAGS := -X github.com/NikunjVaghasiya01/blockbook/common.version=$(VERSION) -X github.com/NikunjVaghasiya01/blockbook/common.gitcommit=$(GITCOMMIT) -X github.com/NikunjVaghasiya01/blockbook/common.buildtime=$(BUILDTIME)
BLOCKBOOK_BASE := $(GOPATH)/src/github.com/NikunjVaghasiya01
BLOCKBOOK_SRC := $(BLOCKBOOK_BASE)/blockbook
ARGS ?=

all: build tools

build: prepare-sources
	cd $(BLOCKBOOK_SRC) && go build -tags rocksdb_6_16 -o $(CURDIR)/blockbook -ldflags="-s -w $(LDFLAGS)" $(ARGS)
	cp $(CURDIR)/blockbook /out/blockbook
	chown $(PACKAGER) /out/blockbook

build-debug: prepare-sources
	cd $(BLOCKBOOK_SRC) && go build -tags rocksdb_6_16 -o $(CURDIR)/blockbook -ldflags="$(LDFLAGS)" $(ARGS)
	cp $(CURDIR)/blockbook /out/blockbook
	chown $(PACKAGER) /out/blockbook

tools:
	cp $(CURDIR)/{ldb,sst_dump} /out
	chown $(PACKAGER) /out/{ldb,sst_dump}

test: prepare-sources
	cd $(BLOCKBOOK_SRC) && go test -tags 'rocksdb_6_16 unittest' `go list ./... | grep -vP '^github.com/NikunjVaghasiya01/blockbook/(contrib|tests)'` $(ARGS)

test-integration: prepare-sources
	cd $(BLOCKBOOK_SRC) && go test -tags 'rocksdb_6_16 integration' `go list github.com/NikunjVaghasiya01/blockbook/tests/...` $(ARGS)

test-all: prepare-sources
	cd $(BLOCKBOOK_SRC) && go test -tags 'rocksdb_6_16 unittest integration' `go list ./... | grep -v '^github.com/NikunjVaghasiya01/blockbook/contrib'` $(ARGS)

prepare-sources:
	@ [ -n "`ls /src 2> /dev/null`" ] || (echo "/src doesn't exist or is empty" 1>&2 && exit 1)
	rm -rf $(BLOCKBOOK_SRC)
	mkdir -p $(BLOCKBOOK_BASE)
	cp -r /src $(BLOCKBOOK_SRC)
	cd $(BLOCKBOOK_SRC) && go mod download
