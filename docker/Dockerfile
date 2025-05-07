FROM tomcat:9-jdk11-openjdk

# 1. Limpeza completa do ambiente
RUN rm -rf /usr/local/tomcat/webapps/* && \
    rm -f /usr/local/tomcat/conf/tomcat-users.xml

# 2. Baixar e extrair o Jolokia
ENV JOLOKIA_VERSION=1.7.2
RUN mkdir -p /usr/local/tomcat/webapps/jolokia && \
    curl -fL https://repo1.maven.org/maven2/org/jolokia/jolokia-war/${JOLOKIA_VERSION}/jolokia-war-${JOLOKIA_VERSION}.war -o /tmp/jolokia.war && \
    unzip /tmp/jolokia.war -d /usr/local/tomcat/webapps/jolokia && \
    rm /tmp/jolokia.war

# 3. Ajuste web.xml e do contexto aberto do Jolokia
RUN sed -i '/<auth-constraint>/,/<\/auth-constraint>/d' /usr/local/tomcat/webapps/jolokia/WEB-INF/web.xml && \
    sed -i '/<security-constraint>/,/<\/security-constraint>/d' /usr/local/tomcat/webapps/jolokia/WEB-INF/web.xml

COPY context.xml /usr/local/tomcat/webapps/jolokia/META-INF/

# 5. Adicionar Jenkins
COPY jenkins.war /usr/local/tomcat/webapps/jenkins.war

EXPOSE 8080

CMD ["catalina.sh", "run"]
