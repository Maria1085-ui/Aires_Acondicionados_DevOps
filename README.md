# Aires_Acondicionados_DevOps
[![CI](https://github.com/Maria1085-ui/aires-exito-devops/actions/workflows/ci.yml/badge.svg)](https://github.com/Maria1085-ui/aires-exito-devops/actions/workflows/ci.yml)
[![Despliegue](https://github.com/Maria1085-ui/aires-exito-devops/actions/workflows/deploy.yml/badge.svg)](https://github.com/Maria1085-ui/aires-exito-devops/actions/workflows/deploy.yml)

**Informe web publicado:** https://jorgeahernandezc80.github.io/aires-exito-devops/
**Google colab EA1:** https://colab.research.google.com/drive/1-CFBAONsdiQ56ubvZLhtT
87AO2OXHXw?usp=sharing

Proyecto del curso *Programación para Análisis de Datos* (PREICA2602B020048), programa de
Ingeniería de Software y Datos, Institución Universitaria Digital de Antioquia. Parte del trabajo
hecho en grupo en la Evidencia 1 y lo convierte en un proyecto de software con **Git, GitHub,
pruebas automáticas, integración continua (CI) y despliegue continuo (CD)**.

## Índice
1. [Integrantes](#integrantes)
2. [Evidencias](#evidencias)
3. [Qué hace el proyecto](#qué-hace-el-proyecto)
4. [Resultados de referencia del grupo](#resultados-de-referencia-del-grupo)
5. [Estructura del repositorio](#estructura-del-repositorio)
6. [Cómo ejecutarlo](#cómo-ejecutarlo)
7. [Pipeline CI/CD](#pipeline-cicd)
8. [Flujo de trabajo con Git](#flujo-de-trabajo-con-git)
9. [Consideraciones éticas y limitaciones](#consideraciones-éticas-y-limitaciones)

## Integrantes
- Lady Laura Olmos
- Selenis Lorena Ramos Guardo
- María Isabel Atencia Ramos
- Jorge Andrés Hernández

Docente: Andrés Felipe Palacio Ángel.

## Evidencias
| Evidencia | Qué se hizo | Dónde está |
|---|---|---|
| **EA1**: análisis y herramientas de extracción de datos | Notebook de Google Colab que extrae los aires acondicionados de Éxito.com con Selenium y Beautiful Soup | [`legacy/`](legacy/README.md) |
| **EA2**: optimizando la productividad en el mundo del software | Código modular en un paquete de Python, control de versiones con Git y GitHub, pruebas con pytest, CI y CD con GitHub Actions | Todo este repositorio |

## Qué hace el proyecto
1. **Extrae** el listado completo de la categoría: Selenium carga cada página y hace scroll; Beautiful Soup lee las tarjetas.
2. **Limpia** con pandas y crea variables: capacidad en BTU, marca, tipo (inverter o convencional), **categoría real** (aire acondicionado, ventilador, enfriador, accesorio u otro) y descuento.
3. **Analiza** precios por marca, tipo, capacidad y categoría, y mide descuentos y publicidad.
4. **Publica** un informe web en GitHub Pages, que se actualiza solo cuando cambia `main`.

## Resultados de referencia del grupo
Valores de la extracción que el grupo hizo el 8 de noviembre de 2026 en su repositorio
([JorgeAHernandezC80/aires-exito-devops](https://github.com/JorgeAHernandezC80/aires-exito-devops)):

- 201 productos distintos en 16 páginas del listado.
- Solo **101 son equipos de aire acondicionado**; el resto son enfriadores pequeños (37), otros artículos (27), ventiladores (25) y accesorios o repuestos (11).
- Mediana de precio de un equipo: **$1.749.900 COP**, con precios de $827.955 a $5.519.900.
- Mediana *inverter*: $1.929.900 (53 equipos); convencional: $1.416.937 (30 equipos).
- El 88,6 % de los productos muestra un precio anterior (oferta visible).

Esta idea de separar los equipos reales de los demás artículos está implementada aquí en
`cleaning.detectar_categoria`. Los datos de `data/productos_demo.csv` son **sintéticos**; los reales
se generan con el workflow *Scraping programado*.

## Estructura del repositorio
```
src/exito_scraper/
  config.py       constantes (URL, tiempos, marcas conocidas)
  parser.py       Beautiful Soup: extrae los datos de cada tarjeta <article>
  driver.py       Chrome headless con Selenium y scroll
  scraper.py      paginación y control de duplicados (no depende del navegador)
  cleaning.py     BTU, marca, tipo, categoría y descuento
  analysis.py     estadística descriptiva y gráficos
  report.py       construye el sitio estático para GitHub Pages
  cli.py          python -m exito_scraper {scrape|demo-data|analyze|build-site}
tests/            36 pruebas con pytest y un HTML de ejemplo (fixtures/)
data/             datos de demostración
legacy/           referencia al notebook original de la EA1
docs/             GIT_FLOW.md · FLUJO_DEVOPS.md · BITACORA.md
.github/workflows/  ci.yml · deploy.yml · scrape.yml
```

## Cómo ejecutarlo
```bash
python -m venv .venv && source .venv/bin/activate      # Windows: .venv\Scripts\activate
pip install -r requirements-dev.txt && pip install -e .
pytest                                                 # 36 pruebas, cobertura mínima 80 %
ruff check .                                           # estilo del código
python -m exito_scraper demo-data                      # datos sintéticos
python -m exito_scraper build-site --entrada data/productos_demo.csv --salida site --demo
python -m exito_scraper scrape                         # scraping REAL (necesita Google Chrome)
```

## Pipeline CI/CD
| Workflow | Disparador | Qué hace |
|---|---|---|
| `ci.yml` | push y PR a `main` y `develop` | ruff → pytest en Python 3.10, 3.11 y 3.12 → prueba de humo |
| `deploy.yml` | push a `main` | pruebas → construye el sitio → publica en GitHub Pages |
| `scrape.yml` | lunes 12:00 UTC o manual | scraping real → commit de `data/productos.csv` → redespliegue |

Explicación detallada en [`docs/FLUJO_DEVOPS.md`](docs/FLUJO_DEVOPS.md).

## Flujo de trabajo con Git
Ramas `main` (estable), `develop` (integración) y ramas de trabajo (`feature/`, `ci/`, `docs/`),
commits con *Conventional Commits*, fusiones `--no-ff` y versiones con etiquetas SemVer.
Ver [`docs/GIT_FLOW.md`](docs/GIT_FLOW.md) y el registro de decisiones en [`docs/BITACORA.md`](docs/BITACORA.md).

## Consideraciones éticas y limitaciones
- Solo se extraen datos públicos de productos, sin datos personales, con pausas de 2 a 4 s entre páginas. Antes de un uso más amplio hay que revisar `robots.txt` y los términos del sitio. Uso académico.

