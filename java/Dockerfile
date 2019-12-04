
FROM oracle/graalvm-ce:19.2.0 as builder

LABEL version="1.0" \
      maintainer="fmasood@redhat.com"



RUN yum -y update && gu install native-image
#RUN mkdir -p /root/java/knative-helloworld
WORKDIR /root/java

#install maven
RUN curl -O http://mirrors.ibiblio.org/apache/maven/maven-3/3.6.3/binaries/apache-maven-3.6.3-bin.tar.gz
RUN tar xzf apache-maven-3.6.3-bin.tar.gz -C /usr/local
RUN ln -s /usr/local/apache-maven-3.6.3 /usr/local/maven
ENV PATH=${PATH}:/usr/locl/maven/bin


RUN /usr/local/maven/bin/mvn io.quarkus:quarkus-maven-plugin:1.0.1.Final:create \
    -DprojectGroupId=com.faisal \
    -DprojectArtifactId=knative-helloworld \
    -DclassName="com.faisal.Hello" \
    -Dpath="/"


COPY ./*.java /root/java/knative-helloworld/src/main/java/com/faisal/

RUN cd knative-helloworld/ && /usr/local/maven/bin/mvn package -Pnative  -DskipTests=true 
#RUN cd knative-helloworld/ && /usr/local/maven/bin/mvn package -Pnative -Dquarkus.native.container-build=true -DskipTests=true 




FROM registry.fedoraproject.org/fedora-minimal:32
RUN groupadd appgroup && useradd appuser -G appgroup
COPY --from=builder /root/java/knative-helloworld/target/*-runner /app/application
WORKDIR /app
USER appuser
CMD ["./application", "-Dquarkus.http.host=0.0.0.0"]