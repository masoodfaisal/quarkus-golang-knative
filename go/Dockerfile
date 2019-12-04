FROM registry.fedoraproject.org/fedora:32 as builder

LABEL version="1.0" \
      maintainer="fmasood@redhat.com"

ENV GOPATH /root/go

RUN yum -y update && yum -y install golang git dep
RUN mkdir -p /root/go/src/github.com/example-inc/knative-helloworld

WORKDIR /root/go/src/github.com/example-inc/knative-helloworld
COPY ./*.go /root/go/src/github.com/example-inc/knative-helloworld/

ENV ROOT_PACKAGE github.com/example-inc/knative-helloworld

RUN dep init && dep ensure -v
RUN CGO_ENABLED=0 GOOS=linux go build -a -installsuffix cgo -o main .


FROM registry.fedoraproject.org/fedora-minimal:32
RUN groupadd appgroup && useradd appuser -G appgroup
COPY --from=builder /root/go/src/github.com/example-inc/knative-helloworld/main /app/
WORKDIR /app
USER appuser
CMD ["./main"]