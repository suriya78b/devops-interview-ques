ARG jfrog_baseimages_url

ARG user_repo

FROM ${jfrog_baseimages_url}kla:alpine3.20.2-jdk21-mvn-v1 as builder

 

RUN mkdir -p /opt/smarts

WORKDIR /opt/smarts

 

COPY softs/pom.xml .

RUN mvn -e -B dependency:resolve

 

 

COPY softs/src ./src

COPY ./api /opt/api

RUN mvn -e -B package -DskipTests

RUN mkdir -p /opt/smarts/target/softs && cd /opt/smarts/target/softs && jar -xf /opt/smarts/target/softs.war

 

FROM ${user_repo}:sea as sea

 

FROM ${jfrog_baseimages_url}kla:tomcat10_jre21-v1 as runtime

 

COPY softs/config/server.xml /usr/local/tomcat/conf/

COPY softs/config/Catalina/localhost/rewrite.config /usr/local/tomcat/conf/Catalina/localhost

COPY --from=builder /opt/smarts/target/softs /usr/local/tomcat/webapps/softs

COPY --from=sea /opt/smarts/ang-sea/dist/ ${CATALINA_HOME}/webapps/sea/

COPY softs/docker/container/runtime.sh .

RUN chmod +x runtime.sh

ENTRYPOINT ["./runtime.sh"]

 

# Stage: Run Whitesource scan on built code

FROM builder as ws-scan

 

ARG jfrog_utils_url

ARG jfrog_key

ARG ws_project_name

ENV ws_project_name $ws_project_name

ARG timeout

ENV timeout $timeout

ARG apiKey

ENV apiKey $apiKey

ARG userKey

ENV userKey $userKey

ARG productToken

ENV productToken $productToken

 

WORKDIR /opt

 

# download the WS jar and config files from Jfrog artifactory

RUN wget --header="X-JFrog-Art-Api: ${jfrog_key}" ${jfrog_utils_url}/whitesource/wss-unified-agent.jar -P .

 

ENTRYPOINT ["/bin/sh", "-c", "timeout $timeout java -jar wss-unified-agent.jar -d /opt/smarts -project ${ws_project_name} -apiKey ${apiKey} -userKey ${userKey} -productToken ${productToken}"]

 
