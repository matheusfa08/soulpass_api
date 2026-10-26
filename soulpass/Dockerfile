# ---------- Etapa 1: compilar o projeto ----------
# (na Render a pasta target/ não existe, pois não vai para o git; por isso o build acontece aqui dentro)
FROM eclipse-temurin:25-jdk AS build

WORKDIR /build

# O mvnw baixa o Maven em .zip e precisa do unzip
RUN apt-get update && apt-get install -y --no-install-recommends unzip \
    && rm -rf /var/lib/apt/lists/*

COPY . .

# Garante final de linha LF e permissão de execução no mvnw (vindo do Windows/git)
RUN sed -i 's/\r$//' mvnw && chmod +x mvnw \
    && ./mvnw -B -DskipTests clean package

# ---------- Etapa 2: executar a aplicação ----------
FROM eclipse-temurin:25-jre

ENV LANG='pt_BR.UTF-8'
ENV LANGUAGE='pt_BR:pt'
ENV TZ='America/Sao_Paulo'

WORKDIR /app

COPY --from=build /build/target/*.jar app.jar

EXPOSE 8080

# MaxRAMPercentage evita que a JVM estoure a memória do plano gratuito da Render
# user.language/country/timezone fazem a JVM usar português do Brasil e o horário de Brasília
ENTRYPOINT ["java", "-XX:MaxRAMPercentage=75.0", "-Duser.language=pt", "-Duser.country=BR", "-Duser.timezone=America/Sao_Paulo", "-jar", "app.jar"]