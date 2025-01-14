.PHONY: help
help:
	@echo "Available make commands:"
	@cat Makefile | grep '^[a-z][^:]*:' | cut -d: -f1 | sort | sed 's/^/  /'

rundep=go run -modfile ../misc/devdeps/go.mod

.PHONY: build
build:
	go build -o build/gno ./cmd/gno

.PHONY: install
install:
	go install ./cmd/gno

.PHONY: clean
clean:
	rm -rf build

########################################
# Dev tools
.PHONY: lint
lint:
	golangci-lint run --config ../.github/golangci.yml ./...

.PHONY: fmt
GOFMT_FLAGS ?= -w
fmt:
	$(rundep) mvdan.cc/gofumpt $(GOFMT_FLAGS) .
	$(rundep) mvdan.cc/gofumpt $(GOFMT_FLAGS) `find stdlibs -name "*.gno"`

########################################
# Test suite
.PHONY: test
test: _test.cmd _test.pkg _test.gnolang

.PHONY: _test.cmd
_test.cmd:
	go test ./cmd/... -v -p 1 -timeout=30m

.PHONY: _test.pkg
_test.pkg:
	go test ./pkg/... -v -p 1 -timeout=30m

.PHONY: _test.gnolang
_test.gnolang: _test.gnolang.native _test.gnolang.stdlibs _test.gnolang.realm _test.gnolang.pkg0 _test.gnolang.pkg1 _test.gnolang.pkg2 _test.gnolang.other
_test.gnolang.other:;        go test tests/*.go -v -run "(TestFileStr|TestSelectors)"
_test.gnolang.realm:;        go test tests/*.go -v -run "TestFiles/^zrealm" --timeout 30m
_test.gnolang.pkg0:;         go test tests/*.go -v -run "TestPackages/(bufio|crypto|encoding|errors|internal|io|math|sort|std|stdshim|strconv|strings|testing|unicode)" --timeout 30m
_test.gnolang.pkg1:;         go test tests/*.go -v -run "TestPackages/regexp" --timeout 30m
_test.gnolang.pkg2:;         go test tests/*.go -v -run "TestPackages/bytes" --timeout 30m
_test.gnolang.native:;       go test tests/*.go -v -test.short -run "TestFilesNative/" --timeout 30m
_test.gnolang.stdlibs:;      go test tests/*.go -v -test.short -run 'TestFiles$$/' --timeout 30m
_test.gnolang.native.sync:;  go test tests/*.go -v -test.short -run "TestFilesNative/" --timeout 30m --update-golden-tests
_test.gnolang.stdlibs.sync:; go test tests/*.go -v -test.short -run 'TestFiles$$/' --timeout 30m --update-golden-tests

########################################
# Code gen
stringer_cmd=$(rundep) golang.org/x/tools/cmd/stringer
.PHONY: _dev.stringer
_dev.stringer:
	$(stringer_cmd) -type=Kind       ./pkg/gnolang
	$(stringer_cmd) -type=Op         ./pkg/gnolang
	$(stringer_cmd) -type=TransCtrl  ./pkg/gnolang
	$(stringer_cmd) -type=TransField ./pkg/gnolang
	$(stringer_cmd) -type=VPType     ./pkg/gnolang
	$(stringer_cmd) -type=Word       ./pkg/gnolang

# genproto:
# see top-level Makefile.
