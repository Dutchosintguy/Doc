# 0x05 - Installing from source

To install from source you will need Go installed and set `$GOPATH` and `$GOBIN` properly.

After that, just run:

```bash
go get -u github.com/gocaio/goca
cd $GOPATH/src/github.com/gocaio/goca
# Make sure that you have GO111MODULE enabled
export GO111MODULE=on
go get ./...
go run goca/goca.go -term "Chema" -loglevel debug
```