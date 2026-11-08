# health-data-etl-lab

Programa práctico de 8 semanas de ciencia de datos en salud: un pipeline ETL que lleva historias clínicas sintéticas de **Synthea** a capas **bronze → silver → gold**, a **HL7 FHIR R4**, a terminologías **SNOMED CT-AU / LOINC** vía el **NCTS** australiano, al **OMOP CDM v5.4**, y termina en un **modelo de reingreso a 30 días** reportado con la estructura de **TRIPOD+AI**.

Todo corre gratis en **Google Colab**, sin Docker.

## Semanas

1. `S01_synthea_extract_load.ipynb` — Extract & Load a bronze (Parquet), SHA-256, manifiesto y conciliación.
2. `S02_data_quality.ipynb` — Calidad de datos con el marco de Kahn y `pandera`.
3. `S03_medallion_duckdb.ipynb` — Capas silver y gold en DuckDB con pruebas de datos.
4. `S04_fhir_api_extract.ipynb` — API REST FHIR (paginación) y aplanado de Bundles.
5. `S05_terminologies.ipynb` — Verhoeff, Mod 10, `$lookup`, `$validate-code` y `$expand` con ECL en el NCTS.
6. `S06_omop_etl.ipynb` — ETL a OMOP CDM v5.4 con reglas de OHDSI ETL-Synthea y chequeos estilo DQD.
7. `S07_cohort_model.ipynb` — Cohorte sin fuga temporal, validación agrupada por paciente, calibración y ficha TRIPOD+AI.
8. `S08_pipeline_end_to_end.ipynb` — Paquete `healthetl`, `pytest`, manifiesto de ejecución y GitHub Actions.

## Cómo empezar

1. Sube este repo a tu GitHub.
2. En Colab: **Archivo → Abrir notebook → GitHub** y elige `S01`.
3. La primera celda monta tu Google Drive; los datos se guardan en `MyDrive/health-data-etl-lab/data/` y persisten entre sesiones.
4. Semana 5: guarda `NCTS_CLIENT_ID` y `NCTS_CLIENT_SECRET` en **Colab → Secrets** (icono de llave). Nunca en el código.
5. Ejecuta los notebooks en orden: cada semana usa lo que produjo la anterior.

## Reglas del repo

- `data/` nunca se versiona (ver `.gitignore`), aunque los datos sean sintéticos.
- Ningún secreto en notebooks ni en el historial de git.
- Un commit por semana con mensaje *Conventional Commits* (`feat(s01): ...`).

## Datos

Synthea sample data (MITRE), CSV y FHIR R4, noviembre 2021: 1.163 pacientes en CSV, 555 en FHIR. Datos sintéticos, sin pacientes reales. Walonoski J. et al., *JAMIA* 2018;25(3):230–238.

## Probado con

Python 3.13 en dos combinaciones: pandas 2.2 / DuckDB 1.1 / scikit-learn 1.6 / pandera 0.24, y pandas 3.0 / DuckDB 2.0 / scikit-learn 1.9 / pandera 0.35. Las llamadas a HAPI FHIR y NCTS se probaron contra servidores simulados que imitan el estándar.

## Aviso

Material educativo. El modelo de la semana 7 no debe usarse para decisiones clínicas.
