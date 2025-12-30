# --- Build Stage ---
FROM maven:3.9.9-eclipse-temurin-17 AS build
WORKDIR /app
COPY pom.xml .
COPY src ./src
RUN mvn -B -q clean package -DskipTests

# --- Deploy Stage ---
FROM tomcat:10.1-jdk17-temurin

# timezone
ENV TZ=Asia/Seoul
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone

RUN groupadd -r appuser \
 && useradd -r -g appuser -d /usr/local/tomcat -s /sbin/nologin appuser \
 && rm -rf /usr/local/tomcat/webapps/* \
 && mkdir -p /usr/local/tomcat/webapps \
 && chown -R appuser:appuser /usr/local/tomcat

RUN mkdir -p /app/data/temp /app/data/upload \
 && chown -R appuser:appuser /app/data \
 && chmod -R 775 /app/data

VOLUME ["/app/data"]
VOLUME ["/usr/local/tomcat/logs"]

COPY --chown=appuser:appuser --from=build /app/target/*.war /usr/local/tomcat/webapps/ROOT.war

USER appuser
EXPOSE 8080
CMD ["catalina.sh", "run"]