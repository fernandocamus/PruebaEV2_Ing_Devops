# 1: Descarga y cacheo de dependencias
FROM eclipse-temurin:21-jdk AS dependencies
WORKDIR /app
COPY pom.xml .
COPY mvnw .
COPY .mvn .mvn
RUN chmod +x mvnw
RUN ./mvnw dependency:go-offline -B

# 2: Compilación y empaquetado del artefacto
FROM eclipse-temurin:21-jdk AS compile
WORKDIR /app
COPY . .
COPY --from=dependencies /root/.m2 /root/.m2
RUN chmod +x mvnw
RUN ./mvnw clean package -DskipTests

# 3: Imagen final
FROM eclipse-temurin:21-jre-alpine AS prod
WORKDIR /app
# Copia únicamente el archivo JAR generado
COPY --from=compile /app/target/*.jar /app/app.jar
# Puerto configurado en application.yml
EXPOSE 9005
CMD ["java", "-jar", "/app/app.jar"]