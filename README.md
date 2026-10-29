# Pipeline-Despliegue-DOY0101

Evaluación Parcial N°1 Ingeniería DevOps: Pipeline de Despliegue

## Descripción del proyecto

Microservicio de catálogo de productos TCG (Trading Card Game) construido con Java 21 y Spring Boot, gestionado con Maven. El repositorio incluye un pipeline de CI/CD implementado con GitHub Actions que compila la aplicación y la despliega en una instancia EC2 de AWS.

## Convenciones de ramas (naming)

| Tipo de rama | Uso | Ejemplo real en el repositorio |
|---|---|---|
| `main` | Rama de producción; todo cambio llega por Pull Request | `main` |
| `develop` | Rama de integración de funcionalidades | `develop` |
| `feature/<descripcion-kebab-case>` | Nuevas funcionalidades | `feature/catalogo-productos-singles`, `feature/triggers-pipeline-ci` |
| `hotfix/<descripcion-kebab-case>` | Correcciones urgentes | `hotfix/corregir-variable-datasource` |

Reglas:
- Los nombres se escriben en minúsculas y con guiones (`kebab-case`).
- Una rama de funcionalidad o corrección es corta: se crea por tarea, se mergea y luego se elimina.
- Nunca se hace *push* directo a `main` ni a `develop`.

## Convenciones de commits

Se utiliza [Conventional Commits](https://www.conventionalcommits.org/), con mensajes descriptivos en español que siguen el formato:

```
<tipo>: <descripción breve e imperativa>
```

Tipos usados en el repositorio:

| Tipo | Significado | Ejemplo real |
|---|---|---|
| `feat:` | Nueva funcionalidad | `feat: agregar mas productos` |
| `hotfix:` | Corrección urgente en producción | `hotfix: corregir variable de entorno datasource url a db_url` |
| `docs:` | Cambios de documentación | `docs: documentar convenciones del repositorio` |
| `fix:` | Corrección de errores | `fix: corregir error en el listado de productos` |
| `refactor:` | Reestructuración de código sin cambiar comportamiento | `refactor: simplificar servicio de productos` |
| `test:` | Agregar o modificar pruebas | `test: agregar pruebas del controlador` |
| `chore:` | Tareas de mantenimiento o configuración | `chore: actualizar dependencias` |

Los commits de merge se generan automáticamente por GitHub con el formato `Merge pull request #N from <rama-origen>`.

## Flujo de merge

El flujo real aplicado en el repositorio se basa en Pull Requests siempre:

1. Se crea una rama `feature/*` o `hotfix/*` desde `main` (o desde `develop` cuando aplica).
2. Se realiza el desarrollo y se abren Pull Requests.
3. **Ramas `feature/*` y `hotfix/*`** se integran mediante PR directo a `main` (ejemplos: PR #1, #2 y #3).
4. **`develop`** consolida la integración y se conecta a `main` mediante un PR (ejemplo: PR #4).
5. Todo merge a `main` se hace únicamente a través de Pull Request, nunca con *push* directo.

## Estrategia de revisión

- **Pull Request obligatorio**: todo cambio, por mínimo que sea, debe pasar por un PR hacia `main` o `develop`.
- **Título y descripción descriptivos**: el PR debe explicar qué se cambia y por qué.
- **Pipeline como verificación**: el workflow se ejecuta con cada push a `main`/`develop` y en todo PR hacia `main`, compilando el microservicio con el Maven Wrapper (`./mvnw -B clean package`).
- **Revisión previa al merge**: se verifica que la compilación sea exitosa y se revisan los cambios antes de aprobar la integración.
- **Historial trazable**: los merges quedan documentados en el historial con referencia al número de PR (merge #1 al #4).

## Pipeline CI/CD

El workflow `.github/workflows/deploy-tcg.yml`:

1. Se dispara en **push a `main` o `develop`** y en **Pull Requests hacia `main`** (además de ejecución manual).
2. Compila el proyecto con Java 21 y el Maven Wrapper.
3. Solo ante **push a `main`**, copia el artefacto generado a la instancia EC2 mediante `scp`, reinicia el servicio con `systemctl` y verifica su estado.
4. Las credenciales de acceso (host, usuario, llave SSH) se gestionan como *secrets* del repositorio.

## Herramientas de IA utilizadas

En el desarrollo de este proyecto se utilizó **Google Gemini** como apoyo:

- **Comandos de terminal Ubuntu**: se consultaron los comandos necesarios para compilar con el Maven Wrapper, copiar artefactos a la instancia EC2 (`scp`/`ssh`) y gestionar el servicio (`systemctl`).
- **Desarrollo del proyecto**: asistencia para resolver dudas durante la creación y configuración del microservicio y el pipeline de despliegue.
- **Documentacion del proyecto**: asistencia para mejorar la redacción de la documentación del proyecto.

## Reflexiones personales sobre el proyecto

- Deymon González: este proyecto me ayudó a entender las diferencias y ventajas que hay entre hacer el despliegue de cambios de un backend de forma totalmente manual y hacerlo de manera automatizada utilizando un pipeline con GitHub Actions. Ya que de esta ultima forma resulta mucho más fácil y útil, por lo cual nos prepara también para agregarlo en nuestros otros proyectos.
- Fernando Camus: Personalmente gracias a los repasos y al haber dado ya una asignatura similiar, pude profundizar y realmente probar que sabia utilizar AWS, además de realizar de manera correcta el CI/CD y comprender bien como utilizar las ramas y los pull requests.
