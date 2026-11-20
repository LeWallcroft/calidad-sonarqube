# Dulce Miel — demostración de SonarQube con GitHub Actions

Este repositorio es una copia independiente para demostrar el análisis de calidad de SonarQube Community Build desde GitHub Actions. No modifica el repositorio del proyecto final. Contiene el código de backend y frontend necesario para el análisis; omite archivos `.env`, dependencias descargadas, resultados de compilación y los workflows de QA/Jenkins del repositorio original.

## Flujo

Al subir cambios a `main`, o al iniciar **Actions → SonarQube local analysis → Run workflow**, el runner de Windows:

1. Compila y ejecuta las pruebas Maven del backend. Las pruebas de integración usan Testcontainers y necesitan Docker Desktop iniciado.
2. Ejecuta las pruebas Angular en Chrome headless y genera cobertura LCOV.
3. Compila el frontend.
4. Envía el análisis y los reportes de cobertura al servidor SonarQube indicado; espera el resultado del Quality Gate.

El workflow no se ejecuta en `pull_request`: este es un repositorio público y el runner tiene acceso a la laptop donde corre SonarQube.

## Configuración requerida

1. En SonarQube, crea un proyecto manual con la clave `calidad-sonarqube` y genera un token de análisis para ese proyecto.
2. En **Settings → Secrets and variables → Actions**, agrega:
   - Secret `SONAR_TOKEN`: el token de análisis. No lo guardes en el repositorio ni lo compartas.
   - `SONAR_HOST_URL` es opcional cuando el servidor está en la misma laptop; el workflow usa `http://localhost:9000` por defecto. Si tu servidor está en otra dirección, define la variable con esa URL.
3. En **Settings → Actions → Runners**, añade un runner autoalojado **Windows x64** y configúralo en la laptop. Ejecuta `run.cmd` para mantenerlo escuchando mientras se prueba.
4. Mantén iniciados Docker Desktop y SonarQube, y asegúrate de que Chrome esté instalado.
5. Ejecuta el workflow manualmente desde la pestaña **Actions** o haz push a `main`.

El token ya configurado en otro repositorio no se copia automáticamente: crea el secreto en este repo. Si cambias de laptop o servidor, actualiza `SONAR_HOST_URL` con una dirección que el runner pueda alcanzar.
