# Arquitectura de Integración para la Modernización de un Core Bancario

**Autor:** Fernando Caranqui
**Fecha:** 28 de julio de 2026

Ejercicio práctico de arquitectura de integración. Diseño de una solución que permite a un banco
tradicional modernizar sus servicios digitales sin reemplazar el core bancario que sostiene su
operación.

---

## El problema

Un banco tradicional necesita integrar seis sistemas: su core existente más un nuevo core digital,
banca web y móvil, una plataforma de pagos, APIs de terceros bajo Open Finance, gestión de riesgos
y prevención de fraudes.

El alcance es de **integración, no de sustitución**. Ambos cores deben coexistir durante un
horizonte plurianual, de modo que el problema central no es construir servicios nuevos, sino
**gobernar la convivencia de dos sistemas de registro transaccionales** sin que esa dualidad se
traslade a los clientes, a los terceros ni a los equipos de desarrollo.

## La propuesta en una línea

Reparto **por producto** entre ambos cores, resuelto por catálogo consultable, con puertos
canónicos que hacen invisible la dualidad fuera de la capa de mediación y doble ingesta de cambios
para que también las operaciones presenciales sean visibles al resto del sistema.

---

## Contenido del repositorio

| Archivo | Descripción |
|---|---|
| `Arquitectura-Integracion-Core-Bancario.pdf` | Documento completo. Es el entregable principal |
| `diagrams/` | Diagramas C4 en PNG y el archivo fuente editable `.drawio` |
| `README.md` | Este archivo |

### Diagramas

| Archivo | Contenido |
|---|---|
| `c4-nivel1-contexto.png` | Nivel 1 — actores, sistema en alcance y sistemas circundantes |
| `c4-nivel2-sincrono.png` | Nivel 2 — contenedores, vista de invocaciones síncronas |
| `c4-nivel2-eventos.png` | Nivel 2 — contenedores, vista de eventos asíncronos |
| `c4-nivel3-ordenes-pago.png` | Nivel 3 — componentes del servicio de órdenes de pago |
| `secuencia-transferencia.png` | Flujo de transferencia con evaluación de fraude en línea |
| `c4-arquitectura-bancaria.drawio` | Fuente editable de los diagramas |

El diagrama de contenedores se presenta en dos vistas del mismo conjunto de elementos. La
separación responde a la densidad de relaciones: representarlas todas en un único lienzo
comprometería la legibilidad.

---

## Estructura del documento

| Secciones | Contenido |
|---|---|
| 0 | Resumen ejecutivo |
| 1 – 4 | Contexto, supuestos declarados, principios de arquitectura y decisiones (ADR) |
| 5 – 9 | Diseño: dominios, propiedad del dato, vistas C4 y flujos extremo a extremo |
| 10 – 18 | Patrones, contratos, seguridad y LOPDP, identidad, continuidad, gobierno, migración y observabilidad |
| 19 – 20 | Glosario, índices de consulta y correspondencia de requerimientos |

La sección 20 indica en qué punto del documento se encuentra la respuesta a cada requerimiento.

---

## Decisiones de arquitectura

Las seis decisiones de las que se deriva el resto del diseño:

| ADR | Decisión |
|---|---|
| 001 | Coexistencia multicore mediante reparto por producto |
| 002 | El core tradicional no participa en el camino crítico de lectura |
| 003 | Estilo de integración asíncrono por defecto y consistencia por saga |
| 004 | Ingesta de cambios del core tradicional mediante doble mecanismo |
| 005 | Evaluación de fraude en línea con degradación graduada por umbral |
| 006 | Modelo de despliegue híbrido y selección de proveedor de nube |

Cada una se documenta con su contexto, las alternativas descartadas y sus consecuencias.

---

## Enfoque

Tres criterios guiaron el trabajo:

**Los supuestos se declaran, no se ocultan.** El enunciado no aporta información sobre el core
existente. En lugar de asumirla implícitamente, la sección 2 declara cada supuesto con su
justificación y su impacto en caso de no cumplirse. Los supuestos sobre el sistema legado se
aíslan además tras una capa anticorrupción, de modo que su refutación afecta a la implementación
de un adaptador y no a la estructura de la solución.

**Cada elemento del diseño responde a un problema declarado.** Los patrones que no resuelven una
necesidad identificada no se incorporan, y los que se consideraron y descartaron figuran con su
motivo. La sección 10.3 recoge ocho descartes explícitos.

**Los límites se hacen visibles.** El documento declara qué no resuelve el diseño: la ausencia de
identidad de cliente en el core tradicional, las dependencias que no admiten degradación, y el
hecho de que el sistema legado no puede instrumentarse. Presentar una arquitectura sin puntos
débiles no sería creíble.

---

## Alcance y marcos de referencia

- Notación **C4** para las vistas de contexto, contenedores y componentes
- **BIAN Service Landscape** como marco de referencia semántica y de nomenclatura de servicios
- **OpenAPI** y **AsyncAPI** para los contratos síncronos y de eventos
- **ISO 20022** en la frontera con las redes de pago
- **LOPDP** (Ley Orgánica de Protección de Datos Personales) como marco de cumplimiento
- **Clean Architecture** y **SOLID** aplicados al nivel de componentes

No se entrega código fuente. Los contratos de API, los esquemas de evento y las firmas de puertos
que aparecen en el documento son especificaciones de interfaz, no implementación.