# Repository Guidelines

## Project Structure & Module Organization
- `tracker-lib`: shared domain and Lucene spatial helpers (e.g., `LocationEvent`, `SpatialHelper`).
- `tracker-simulator`: Spring Boot simulator that publishes location updates to Geode; includes KMZ road data in `tracker-simulator/data`.
- `tracker-web-app`: Spring Boot REST API + static OpenLayers UI in `tracker-web-app/src/main/resources/public`.
- `scripts`: helper scripts to start/stop Geode (`startGeode.bat`, `startGeode.sh`).
- `images`: documentation assets used by `README.md`.

## Build, Test, and Development Commands
- `gradlew bootJar`: builds the shared library jar from the root project.
- `gradlew tracker-lib:copyLuceneLibs`: copies Lucene spatial dependencies for the Geode server classpath.
- `scripts/startGeode.bat` or `scripts/startGeode.sh`: starts a local Geode locator and servers with Lucene index setup.
- `gradlew tracker-simulator:bootRun`: runs the simulator and begins pushing location events.
- `gradlew tracker-web-app:bootRun`: starts the web server (default `http://localhost:8080`).
- `gradlew test`: runs unit tests (none are currently present, but use this when adding tests).

## Coding Style & Naming Conventions
- Java 17, 4-space indentation, standard brace style.
- Class names use PascalCase (`GeospatialWebServer`); methods/fields use lower camelCase; constants use `UPPER_SNAKE_CASE`.
- Keep configuration in `*/src/main/resources/config/application.yml` and prefer property names under `demo.*`.
- No formatter or linter is configured; keep diffs small and readable.

## Testing Guidelines
- Testing framework is JUnit 4 (see root `build.gradle`).
- Place tests in `*/src/test/java` and name them `*Test.java`.
- Add tests for simulator logic or Lucene queries when behavior changes.

## Commit & Pull Request Guidelines
- History uses short, imperative commits (e.g., ?Update readme?). Follow that style.
- PRs should include: summary, how to run locally, and screenshots for UI changes (OpenLayers map).

## Configuration & Local Setup Notes
- Apache Geode must be installed locally; see `README.md` for setup steps.
- No Maven credentials are required for Apache Geode dependencies.
- Locator hosts live in `demo.GeospatialSimulator.locators` and `demo.GeospatialWebServer.locators`.


