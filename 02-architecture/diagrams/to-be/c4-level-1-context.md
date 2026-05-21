# Nivel 1 - Contexto (To-Be)

## Descripcion

Esta vista `to-be` documenta el contexto objetivo del sistema a alto nivel. En este nivel no se descompone la solucion en frontend, backend, base de datos o componentes internos; solo se representa la relacion entre las personas usuarias internas y el sistema veterinario como producto principal.

## Alcance de la vista

- muestra a la persona usuaria principal confirmada;
- mantiene una sola frontera de sistema para la solucion web;
- deja el detalle tecnico para los niveles 2 y 3.

## Elementos esperados

- `Usuario interno de la veterinaria`
  Administrador y personal operativo que usan el sistema desde un navegador.
- `Sistema Web de Gestion Veterinaria`
  Sistema principal consumido por usuarios internos para autenticacion y gestion operativa.

## Relaciones esperadas

- `Usuario interno de la veterinaria -> Sistema Web de Gestion Veterinaria`
  Usa la aplicacion web desde el navegador para autenticarse y gestionar procesos del dominio veterinario.

## Observaciones

- El contexto objetivo conserva el mismo actor principal ya identificado en la implementacion actual.
- Los cambios arquitectonicos relevantes del proyecto se expresan mejor en los niveles 2 y 3.
- Esta vista sirve como marco de referencia para los diagramas `to-be` de contenedores y componentes.
