---
date: 2022-01-22
title: Docker and Openshift
linkTitle: Docker and Openshift
description: >
  
author: Bharat Bhushan
resources:
  - src: "**.{png,jpg}"
    title: "Image"
---


{{< imgproc docker Fill "500x200" >}}
{{< /imgproc >}}

This blog contains codes and snippets related to docker and openshift. Copy the codes from here to run in docker. 

KCS 201 - https://kcs.pages.boeing.com/workshops/kcs201/
Tutorial - https://kcs.pages.boeing.com/docs/tutorial/

Openshift Console - Deploy Image · Red Hat OpenShift
Docker Cheatsheet - https://dockerlabs.collabnix.com/docker/cheatsheet/

Docker images : https://git.web.boeing.com/container/boeing-images


## Docker commands

```
docker login registry.web.boeing.com -u 3477482 -p
docker images
docker ps -a
docker build --build-arg ARTIFACTORY_USERNAME= --build-arg ARTIFACTORY_PASSWORD= -t registry.web.boeing.com/bharat.bhushan/767_ncr/neo4j:1 .
docker search
docker inspect image
docker run -dp 127.0.0.1:3000:3000 getting-started
docker run -d -p 8000:8000 --name flask-app --rm sres.web.boeing.com:5000/3477482/flask-app
docker run -p 7474:7474 -p 7687:7687 -v $pwd/data:/data registry.web.boeing.com/bharat.bhushan/767_ncr/neo4j-5.18:1

docker tag flask_app registry.web.boeing.com/bharat.bhushan/767_ncr/test
docker push registry.web.boeing.com/bharat.bhushan/767_ncr/test
docker system prune
docker exec -it <container_name_or_id> /bin/sh
docker save -o neo4j.tar registry.web.boeing.com/bharat.bhushan/767_ncr/neo4j:2
docker load -i neo4j.tar
```

## Deploying Neo4j community on openshift

### Dockerfile

```
FROM registry.web.boeing.com/container/boeing-images/stack/ubi8minimal-jdk11:latest
ENV NEO4J_HOME="/app/neo4j"
WORKDIR /app
COPY neo4j-community-4.4.7 /app/neo4j
RUN chgrp -R 0 /app && chmod -R g=u /app
ENV PATH "${NEO4J_HOME}"/bin:$PATH
VOLUME /data 
EXPOSE 7687 7474
CMD ["neo4j", "console", "--verbose"]
```

### Docker/Openshift commands

```
docker build --build-arg ARTIFACTORY_USERNAME=3477482 --build-arg ARTIFACTORY_PASSWORD=  -t registry.web.boeing.com/bharat.bhushan/767_ncr/dsrm:1 .
docker push registry.web.boeing.com/bharat.bhushan/767_ncr/dsrm:1
oc login --token= --server=https://api.kcs-pre-clt.k8s.boeing.com:6443![image](https://github.com/bhbharat/my_website/assets/20968466/dcac7e6e-6d1b-464e-8888-0a3539147ce5)
oc new-app registry.web.boeing.com/bharat.bhushan/767_ncr/dsrm:1 --name dsrm
```
1. The neo4j.conf file has 256 max memory. Increase the memory to 1G in openshift web console resource limit.
2. Convert the clusterIP to NodePort by editing the services yaml - oc edit svc dsrm. replace type clusterIP to NodePort.
3. expose the services one by one - oc expose service dsrm --port 7687 --name database, oc expose service dsrm --port 7474 --name neo4j



## openshift commands

```
oc login --token= --server=https://api.kcs-pre-clt.k8s.boeing.com:6443
oc create secret docker-registry gitlab-registry --docker-server=registry.web.boeing.com --docker-username= --docker-password=  --docker-email=bharat.bhushan@boeing.com
oc secrets link default gitlab-registry --for=pull

Oc get projects
Oc get secrets
Oc status
Oc project 767ncr
Oc get all
oc new-app registry.web.boeing.com/bharat.bhushan/767_ncr/streamlit --name streamlit1
Oc get pods
oc expose service streamlit1
oc rsh streamlit-cf5db5b44-5g99f
Oc delete route.route.openshift.io/streamlit1
oc get deployment.apps/streamlit -o yaml
oc adm prune
oc import-image imagestream.image.openshift.io/neo4j:1

Oc get pvc
oc set volume deployment.apps/streamlit --add -t pvc --claim-name=myclaim --mount-path=/
oc delete imagestream,deployment,dc,svc,route,pvc -l app=streamlit

Add SSL
oc edit route.route.openshift.io/streamlit
Add tls:termination: edge below host
spec:
  host: time-cpuser1-test.apps.kcstd-clt.cloud.boeing.com
  port:
    targetPort: 8080-tcp
  tls:
    termination: edge


```

## Dockerfile

```
FROM registry.web.boeing.com/container/boeing-images/stack/ubi8minimal-python3:latest

ARG ARTIFACTORY_USERNAME=
ARG ARTIFACTORY_PASSWORD=
ENV NEO4J_HOME=/var/lib/neo4j
ENV APOC_URL=https://sres.web.boeing.com/artifactory/osstools/neo4j-apoc/4.4.0.5/apoc-4.4.0.5-all.jar
ENV NEO4J_URL=https://sres.web.boeing.com/artifactory/osstools/neo4j/4.4.x/community/neo4j-community-4.4.7-unix.tar.gz
ENV JDK_URL=https://sres.web.boeing.com/artifactory/osstools/openjdk/11.0.1/openjdk-11.0.1_linux-x64_bin.tar.gz

# RUN curl -u$ARTIFACTORY_USERNAME:$ARTIFACTORY_PASSWORD -O $JDK_URL \
#     && tar zxf openjdk-11.0.1_linux-x64_bin.tar.gz && rm -f openjdk-11.0.1_linux-x64_bin.tar.gz
# RUN curl -u$ARTIFACTORY_USERNAME:$ARTIFACTORY_PASSWORD -O $NEO4j_URL \
#     && tar zxf openjdk-11.0.1_linux-x64_bin.tar.gz && rm -f openjdk-11.0.1_linux-x64_bin.tar.gz
# RUN curl -u$ARTIFACTORY_USERNAME:$ARTIFACTORY_PASSWORD -o $NEO4J_HOME/plugins/apoc-4.4.0.5-all.jar $APOC_URL

COPY neo4j-community-4.4.7-unix.tar.gz /neo4j-community-4.4.7-unix.tar.gz

RUN mkdir $NEO4J_HOME \
    && tar xvfz neo4j-community-4.4.7-unix.tar.gz -C $NEO4J_HOME --strip-components=1 \
    && rm -f neo4j-community-4.4.7-unix.tar.gz \
    && chgrp -R 0 $NEO4J_HOME \
    && chmod -R g=u $NEO4J_HOME

COPY openjdk-11.0.1_linux-x64_bin.tar.gz /openjdk-11.0.1_linux-x64_bin.tar.gz
COPY neo4j-community-4.4.7-unix/plugins/apoc-4.4.0.15-all.jar $NEO4J_HOME/plugins/apoc-4.4.0.15-all.jar

RUN tar zxf openjdk-11.0.1_linux-x64_bin.tar.gz && rm -f openjdk-11.0.1_linux-x64_bin.tar.gz
ENV PATH="${PATH}:/jdk-11.0.1/bin"
ENV JAVA_HOME=/jdk-11.0.1

RUN sed -i 's/#dbms.default_listen_address/dbms.default_listen_address/' $NEO4J_HOME/conf/neo4j.conf \
    && sed -i 's/#dbms.default_advertised_address=localhost/dbms.default_advertised_address=0.0.0.0/g' $NEO4J_HOME/conf/neo4j.conf \
    && sed -i '/#dbms.memory.heap.initial_size/c\dbms.memory.heap.initial_size=1G' $NEO4J_HOME/conf/neo4j.conf \
    && sed -i '/#dbms.memory.heap.max_size/c\dbms.memory.heap.max_size=2G' $NEO4J_HOME/conf/neo4j.conf \
    && sed -i '/#dbms.memory.pagecache.size/c\dbms.memory.pagecache.size=1G' $NEO4J_HOME/conf/neo4j.conf \
    && sed -i '/#dbms.security.allow_csv_import_from_file_urls/c\dbms.security.allow_csv_import_from_file_urls=true' $NEO4J_HOME/conf/neo4j.conf \
    && sed -i '/#dbms.security.procedures.unrestricted/c\dbms.security.procedures.unrestricted=algo.*,apoc.*,gds.*' $NEO4J_HOME/conf/neo4j.conf

ENV PATH=$NEO4J_HOME/bin:$PATH

COPY flask /flask

# Install necessary packages for the Flask application
RUN chgrp -R 0 /flask /var/lib/neo4j && chmod -R g=u /flask /var/lib/neo4j \
    && pip3 install --no-cache-dir --index-url="https://$ARTIFACTORY_USERNAME:$ARTIFACTORY_PASSWORD@sres.web.boeing.com/artifactory/api/pypi/pypi-remote/simple" \
    --trusted-host=sres.web.boeing.com \
    -r /flask/requirements.txt \
    && chmod +x /flask/docker-run.sh \
    && mv flask/apoc.conf $NEO4J_HOME/conf/apoc.conf


EXPOSE 5000 7474 7687

CMD ["/flask/docker-run.sh"]

# CMD ["tail", "-f", "/dev/null"]



```


