# Sistema de Seguimiento Académico
## Documento de Proyecto – Especificación, Casos de Uso y Pruebas

---

## 1. Descripción General

El Sistema de Seguimiento Académico permite a una institución educativa gestionar de forma digital el registro de estudiantes y sus calificaciones, calcular promedios, determinar el estado académico y controlar el acceso según el rol del actor.

---

## 2. Actores del Sistema

| Actor | Descripción |
|---|---|
| **Docente** | Personal docente autorizado. Puede registrar estudiantes, ingresar calificaciones y consultar información de cualquier estudiante. |
| **Estudiante** | Alumno matriculado. Solo puede consultar su propia información académica (notas, promedio y estado). |

---

## 3. Reglas de Negocio

### 3.1 Rango de calificaciones
- Las calificaciones se expresan en escala de **0.0 a 5.0**.
- No se aceptan valores negativos ni superiores a 5.0.

### 3.2 Determinación del Estado Académico

| Estado | Condición |
|---|---|
| **APROBADO** | Promedio ≥ 3.5 |
| **EN RIESGO ACADÉMICO** | Promedio ≥ 3.0 y < 3.5 |
| **REPROBADO** | Promedio < 3.0 |
| **SIN CALIFICACIONES** | No se han registrado calificaciones aún |

### 3.3 Control de acceso
- Solo el **Docente** puede registrar estudiantes, ingresar calificaciones y listar todos los estudiantes.
- El **Estudiante** solo puede consultar su propia información (consulta por código, promedio, estado).

---

## 4. Casos de Uso Textuales

---

### CU-01 – Registrar Estudiante

| Campo | Descripción |
|---|---|
| **Identificador** | CU-01 |
| **Nombre** | Registrar Estudiante |
| **Actor principal** | Docente |
| **Precondiciones** | El docente está autenticado en el sistema. |
| **Postcondiciones** | El estudiante queda registrado y disponible para consultas. |

**Flujo principal:**
1. El docente solicita registrar un nuevo estudiante.
2. El sistema solicita: código, nombre completo y grado/programa.
3. El docente ingresa los datos.
4. El sistema valida que el código no esté duplicado.
5. El sistema crea y almacena el registro del estudiante.
6. El sistema confirma el registro exitoso.

**Flujos alternativos:**
- **FA-01** (Código duplicado): En el paso 4, si el código ya existe, el sistema muestra un mensaje de error y solicita un código diferente.
- **FA-02** (Datos incompletos): Si algún campo está vacío o nulo, el sistema rechaza la operación.
- **FA-03** (Rol incorrecto): Si quien intenta registrar no es docente, el sistema lanza un error de acceso denegado.

---

### CU-02 – Consultar Estudiante

| Campo | Descripción |
|---|---|
| **Identificador** | CU-02 |
| **Nombre** | Consultar Estudiante |
| **Actores** | Docente, Estudiante |
| **Precondiciones** | El actor está identificado en el sistema. |

**Flujo principal (Docente):**
1. El docente ingresa el código del estudiante a buscar.
2. El sistema busca el registro.
3. El sistema muestra la información completa del estudiante.

**Flujo principal (Estudiante):**
1. El estudiante ingresa su propio código.
2. El sistema verifica que el código solicitado coincide con el del actor.
3. El sistema muestra únicamente la información del estudiante solicitante.

**Flujos alternativos:**
- **FA-01** (No existe): El sistema informa que no hay ningún estudiante con ese código.
- **FA-02** (Acceso no autorizado): Si un estudiante intenta consultar a otro, el sistema deniega el acceso.

---

### CU-03 – Registrar Calificación

| Campo | Descripción |
|---|---|
| **Identificador** | CU-03 |
| **Nombre** | Registrar Calificación |
| **Actor principal** | Docente |
| **Precondiciones** | El estudiante al que se le asignará la nota ya está registrado. |

**Flujo principal:**
1. El docente ingresa el código del estudiante.
2. El docente ingresa la calificación numérica.
3. El sistema valida que la nota esté en el rango [0.0, 5.0].
4. El sistema agrega la calificación a la lista del estudiante.
5. El sistema confirma el registro.

**Flujos alternativos:**
- **FA-01** (Estudiante no existe): El sistema muestra un mensaje indicando que el código no corresponde a ningún estudiante.
- **FA-02** (Nota fuera de rango): El sistema rechaza la calificación e informa el rango válido.
- **FA-03** (Rol incorrecto): Solo el docente puede registrar calificaciones; cualquier otro actor recibe error de acceso.

---

### CU-04 – Calcular Promedio

| Campo | Descripción |
|---|---|
| **Identificador** | CU-04 |
| **Nombre** | Calcular Promedio |
| **Actores** | Docente, Estudiante |

**Flujo principal:**
1. El actor indica el código del estudiante (o el propio en caso de ser estudiante).
2. El sistema verifica permisos de acceso.
3. El sistema obtiene la lista de calificaciones.
4. El sistema calcula el promedio aritmético.
5. El sistema muestra el promedio con dos decimales.

**Flujos alternativos:**
- **FA-01** (Sin calificaciones): El sistema informa que aún no existen calificaciones registradas para ese estudiante.
- **FA-02** (Acceso no autorizado): Estudiante intenta ver el promedio de otro → error de seguridad.

---

### CU-05 – Determinar Estado Académico

| Campo | Descripción |
|---|---|
| **Identificador** | CU-05 |
| **Nombre** | Determinar Estado Académico |
| **Actores** | Docente, Estudiante |

**Flujo principal:**
1. El actor indica el código del estudiante.
2. El sistema verifica permisos.
3. El sistema calcula el promedio actual.
4. El sistema aplica las reglas de negocio (sección 3.2) y determina el estado.
5. El sistema muestra el estado con su descripción.

**Estados posibles:** APROBADO · EN RIESGO ACADÉMICO · REPROBADO · SIN CALIFICACIONES

**Flujos alternativos:**
- **FA-01** (Sin calificaciones): Estado retornado = SIN_CALIFICACIONES.
- **FA-02** (Acceso no autorizado): igual que CU-04 FA-02.

---

### CU-06 – Listar Estudiantes

| Campo | Descripción |
|---|---|
| **Identificador** | CU-06 |
| **Nombre** | Listar Estudiantes |
| **Actor principal** | Docente |
| **Precondiciones** | El docente está autenticado. |

**Flujo principal:**
1. El docente solicita el listado completo de estudiantes.
2. El sistema recupera todos los registros.
3. El sistema muestra código, nombre, programa, promedio y estado de cada estudiante.

**Flujos alternativos:**
- **FA-01** (Sin estudiantes): El sistema informa que no hay registros.
- **FA-02** (Rol incorrecto): Estudiante intenta listar → acceso denegado.

---

## 5. Estructura del Proyecto

```
sistema-academico/
├── pom.xml
└── src/
    ├── main/java/academic/
    │   ├── model/
    │   │   ├── Estudiante.java          # Entidad principal
    │   │   ├── EstadoAcademico.java     # Enum de estados
    │   │   └── Rol.java                 # Enum de roles (DOCENTE/ESTUDIANTE)
    │   ├── repository/
    │   │   ├── EstudianteRepository.java          # Interfaz
    │   │   └── EstudianteRepositoryMemoria.java   # Implementación en memoria
    │   ├── service/
    │   │   └── SistemaAcademico.java    # Lógica de negocio + control de acceso
    │   └── ui/
    │       └── ConsolaApp.java          # Interfaz de usuario por consola
    └── test/java/academic/
        ├── unit/
        │   ├── EstudianteTest.java           # PU-01: Modelo
        │   ├── EstudianteRepositoryTest.java  # PU-02: Repositorio
        │   └── SistemaAcademicoTest.java      # PU-03: Servicio
        └── acceptance/
            └── AceptacionTest.java            # PA: Pruebas de aceptación
```

---

## 6. Catálogo de Pruebas

### 6.1 Pruebas Unitarias – Modelo Estudiante (PU-01)

| ID | Descripción | Resultado esperado |
|---|---|---|
| PU-01-01 | Crear estudiante con datos válidos | Objeto creado sin errores |
| PU-01-02 | Código nulo | `IllegalArgumentException` |
| PU-01-03 | Nombre vacío | `IllegalArgumentException` |
| PU-01-04 | Programa nulo | `IllegalArgumentException` |
| PU-01-05 | Agregar calificación 0.0 (límite inferior) | Aceptada |
| PU-01-06 | Agregar calificación 5.0 (límite superior) | Aceptada |
| PU-01-07 | Calificación negativa (-0.1) | `IllegalArgumentException` |
| PU-01-08 | Calificación mayor a 5.0 (5.1) | `IllegalArgumentException` |
| PU-01-09 | Promedio sin calificaciones | Retorna 0.0 |
| PU-01-10 | Promedio con una calificación | Promedio = esa calificación |
| PU-01-11 | Promedio con múltiples calificaciones | Media aritmética correcta |
| PU-01-12 | Estado sin calificaciones | SIN_CALIFICACIONES |
| PU-01-13 | Estado con promedio ≥ 3.5 | APROBADO |
| PU-01-14 | Estado con promedio en [3.0, 3.5) | EN_RIESGO |
| PU-01-15 | Estado con promedio < 3.0 | REPROBADO |
| PU-01-16 | Límite exacto 3.5 | APROBADO |
| PU-01-17 | Límite exacto 3.0 | EN_RIESGO |

### 6.2 Pruebas Unitarias – Repositorio (PU-02)

| ID | Descripción | Resultado esperado |
|---|---|---|
| PU-02-01 | Guardar y recuperar estudiante | Recuperación exitosa |
| PU-02-02 | Buscar inexistente | Optional vacío |
| PU-02-03 | Buscar con código nulo | Optional vacío |
| PU-02-04 | Listar vacío | Lista vacía |
| PU-02-05 | Listar todos | Lista con todos los registros |
| PU-02-06 | Existe – código registrado | true |
| PU-02-07 | Existe – código no registrado | false |
| PU-02-08 | Eliminar existente | true, ya no existe |
| PU-02-09 | Eliminar inexistente | false |
| PU-02-10 | Guardar nulo | NullPointerException |

### 6.3 Pruebas Unitarias – Servicio (PU-03)

| ID | CU | Descripción | Resultado esperado |
|---|---|---|---|
| PU-03-01 | CU-01 | Docente registra estudiante | Estudiante creado |
| PU-03-02 | CU-01 | Código duplicado | `IllegalArgumentException` |
| PU-03-03 | CU-01 | Estudiante no puede registrar | `SecurityException` |
| PU-03-04 | CU-02 | Docente consulta existente | Optional presente |
| PU-03-05 | CU-02 | Docente consulta inexistente | Optional vacío |
| PU-03-06 | CU-02 | Estudiante consulta propia info | Optional presente |
| PU-03-07 | CU-02 | Estudiante consulta otro | `SecurityException` |
| PU-03-08 | CU-03 | Docente registra calificación válida | Sin excepción |
| PU-03-09 | CU-03 | Calificación fuera de rango | `IllegalArgumentException` |
| PU-03-10 | CU-03 | Estudiante no puede registrar nota | `SecurityException` |
| PU-03-11 | CU-03 | Calificación a estudiante inexistente | `IllegalArgumentException` |
| PU-03-12 | CU-04 | Promedio correcto | Valor numérico correcto |
| PU-03-13 | CU-04 | Promedio sin calificaciones | `IllegalStateException` |
| PU-03-14 | CU-04 | Estudiante consulta propio promedio | Sin excepción |
| PU-03-15 | CU-04 | Estudiante consulta promedio de otro | `SecurityException` |
| PU-03-16 | CU-05 | Estado APROBADO | APROBADO |
| PU-03-17 | CU-05 | Estado EN_RIESGO | EN_RIESGO |
| PU-03-18 | CU-05 | Estado REPROBADO | REPROBADO |
| PU-03-19 | CU-05 | Estado SIN_CALIFICACIONES | SIN_CALIFICACIONES |
| PU-03-20 | CU-05 | Estudiante consulta propio estado | Sin excepción |
| PU-03-21 | CU-06 | Docente lista estudiantes | Lista completa |
| PU-03-22 | CU-06 | Estudiante intenta listar | `SecurityException` |

### 6.4 Pruebas de Aceptación (PA)

| ID | CU | Escenario | Resultado esperado |
|---|---|---|---|
| PA-CU01-01 | CU-01 | Flujo exitoso de registro | Estudiante recuperable |
| PA-CU01-02 | CU-01 | Código duplicado | Error descriptivo |
| PA-CU01-03 | CU-01 | Rol incorrecto | Acceso denegado |
| PA-CU02-01 | CU-02 | Docente busca existente | Datos correctos |
| PA-CU02-02 | CU-02 | Estudiante no existe | Optional vacío |
| PA-CU02-03 | CU-02 | Estudiante consulta propia info | Datos correctos |
| PA-CU02-04 | CU-02 | Estudiante ve a otro | Acceso denegado |
| PA-CU03-01 | CU-03 | Registrar calificación válida | Nota en lista del estudiante |
| PA-CU03-02 | CU-03 | Calificación máxima (5.0) | Aceptada |
| PA-CU03-03 | CU-03 | Calificación mínima (0.0) | Aceptada |
| PA-CU03-04 | CU-03 | Calificación 5.1 | Rechazada |
| PA-CU03-05 | CU-03 | Calificación negativa | Rechazada |
| PA-CU04-01 | CU-04 | Promedio con varias notas | Resultado correcto |
| PA-CU04-02 | CU-04 | Sin calificaciones | Mensaje adecuado |
| PA-CU05-01 | CU-05 | Estado APROBADO | Correcto |
| PA-CU05-02 | CU-05 | Estado EN RIESGO | Correcto |
| PA-CU05-03 | CU-05 | Estado REPROBADO | Correcto |
| PA-CU05-04 | CU-05 | Sin calificaciones | SIN_CALIFICACIONES |
| PA-CU06-01 | CU-06 | Docente lista estudiantes | Lista con 3 registros |
| PA-CU06-02 | CU-06 | Sin estudiantes | Lista vacía |
| PA-CU06-03 | CU-06 | Estudiante intenta listar | Acceso denegado |
| PA-FLUJO-01 | Todos | Flujo integrado completo | Todos los pasos correctos |

---

## 7. Instrucciones de Ejecución

### Requisitos
- Java 17+
- Maven 3.8+

### Compilar y ejecutar pruebas
```bash
cd sistema-academico
mvn clean test
```

### Generar JAR y ejecutar la aplicación
```bash
mvn clean package
java -jar target/sistema-academico-1.0.0.jar
```

---

## 8. Decisiones de Diseño

| Decisión | Justificación |
|---|---|
| Escala 0.0 – 5.0 | Estándar colombiano de educación superior |
| Umbral EN RIESGO en 3.0–3.5 | Permite intervención temprana antes de reprobar |
| Repositorio como interfaz | Facilita pruebas unitarias con implementación en memoria y futura persistencia en BD |
| Control de acceso en la capa de servicio | Centraliza la lógica de seguridad y evita duplicación |
| `Optional<Estudiante>` en búsqueda | Evita retornos nulos y obliga al cliente a manejar el caso "no encontrado" |
