# health-data-etl-lab

Programa práctico de 10 semanas de ciencia de datos en salud: un pipeline ETL que lleva historias clínicas sintéticas de **Synthea** a capas **bronze → silver → gold**, a **HL7 FHIR R4**, consulta FHIR con SQL mediante vistas estándar **SQL on FHIR**, trabaja terminologías **SNOMED CT-AU / LOINC** vía el **NCTS** australiano (incluidos los temas del curso *Advanced FHIR Terminology* de la ADHA), integra un **extracto de laboratorio** con mapeo LOINC y armonización a unidades SI (UCUM), lo lleva al **OMOP CDM v5.4**, y termina en un **modelo de reingreso a 30 días** reportado con la estructura de **TRIPOD+AI**.

Todo corre gratis en **Google Colab**, sin Docker.

## Semanas

1. `S01_synthea_extract_load.ipynb` — Extract & Load a bronze (Parquet), SHA-256, manifiesto y conciliación.
2. `S02_data_quality.ipynb` — Calidad de datos con el marco de Kahn y `pandera`.
3. `S03_medallion_duckdb.ipynb` — Capas silver y gold en DuckDB con pruebas de datos.
4. `S04_fhir_api_extract.ipynb` — API REST FHIR (paginación) y aplanado de Bundles.
5. `S05_sql_on_fhir.ipynb` — NDJSON tipo `$export`, `ViewDefinition`s de SQL on FHIR con un *view runner* propio comprobado contra las pruebas oficiales, SQL en DuckDB, trampas del SQL directo sobre JSON y criterios para elegir API, `$export`, vistas u OMOP.
6. `S06_terminologies.ipynb` — Verhoeff, Mod 10, `$lookup`, `$validate-code` y `$expand` con ECL en el NCTS; `CodeSystem`, composición de `ValueSet`, fuerza de *binding* de AU Core, `$subsumes`, `$closure`, versiones y doble codificación.
7. `S07_loinc_lab_harmonization.ipynb` — Extracto de laboratorio simulado, ejes LOINC, *crosswalk* con revisión humana, validación NCTS/SPIA, armonización SI con UCUM, cuarentena, precedencia entre fuentes, conformidad con `au-fhir-test-data` de HL7 Australia, y `CodeSystem` + `ConceptMap` con `$translate`.
8. `S08_omop_etl.ipynb` — ETL a OMOP CDM v5.4 con reglas de OHDSI ETL-Synthea, `SOURCE_TO_CONCEPT_MAP` para códigos locales y chequeos estilo DQD.
9. `S09_cohort_model.ipynb` — Cohorte sin fuga temporal, validación agrupada por paciente, calibración y ficha TRIPOD+AI.
10. `S10_pipeline_end_to_end.ipynb` — Paquete `healthetl` (incluye el módulo de laboratorio y el *crosswalk* versionado), `pytest`, manifiesto de ejecución y GitHub Actions.

## Cómo empezar

1. Sube este repo a tu GitHub.
2. En Colab: **Archivo → Abrir notebook → GitHub** y elige `S01`.
3. La primera celda monta tu Google Drive; los datos se guardan en `MyDrive/health-data-etl-lab/data/` y persisten entre sesiones.
4. Semanas 6 y 7: guarda `NCTS_CLIENT_ID` y `NCTS_CLIENT_SECRET` en **Colab → Secrets** (icono de llave). Nunca en el código.
5. Ejecuta los notebooks en orden: cada semana usa lo que produjo la anterior.

## Reglas del repo

- `data/` nunca se versiona (ver `.gitignore`), aunque los datos sean sintéticos. Excepciones controladas, porque son metadatos sin pacientes: las vistas `views/*.json` de la semana 5 y el *crosswalk* LOINC revisado, que se promueve a `src/healthetl/reference/` en la semana 10.
- Ningún secreto en notebooks ni en el historial de git.
- Un commit por semana con mensaje *Conventional Commits* (`feat(s01): ...`).

## Datos

Synthea sample data (MITRE), CSV y FHIR R4, noviembre 2021: 1.163 pacientes en CSV, 555 en FHIR. Datos sintéticos, sin pacientes reales. Walonoski J. et al., *JAMIA* 2018;25(3):230–238.

El extracto del laboratorio de la semana 7 es **simulado** (semilla fija) a partir de Synthea, con un laboratorio ficticio. En tiempo de ejecución, la semana 5 clona `FHIR/sql-on-fhir.js` (solo lee sus pruebas JSON) y la semana 7 clona `hl7au/au-fhir-test-data` (solo lee recursos `Observation`); ninguno de los dos se redistribuye aquí.

## Probado con

Python 3.13 en dos combinaciones: pandas 2.2 / DuckDB 1.1 / scikit-learn 1.6 / pandera 0.24, y pandas 3.0 / DuckDB 2.0 / scikit-learn 1.9 / pandera 0.35; `fhirpathpy` 2.2.4 en ambas. Las llamadas a HAPI FHIR y NCTS (incluidas SPIA, `$subsumes`, `$expand` con ValueSet en línea y el *binding* de AU Core) se probaron contra servidores simulados que imitan el estándar; las respuestas reales del NCTS pueden diferir en detalle.

## Aviso

Material educativo. El modelo de la semana 9 no debe usarse para decisiones clínicas, y las conversiones de unidades de la semana 7 no sustituyen la validación de un laboratorio acreditado.
