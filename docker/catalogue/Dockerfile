FROM golang:1.7

COPY . /go/src/github.com/microservices-demo/catalogue
WORKDIR /go/src/github.com/microservices-demo/catalogue

RUN go get -u github.com/FiloSottile/gvt
RUN gvt restore && \
    CGO_ENABLED=0 GOOS=linux go build -a -installsuffix cgo -o /app github.com/microservices-demo/catalogue/cmd/cataloguesvc
RUN mkdir /files
RUN mv /app /files/app
