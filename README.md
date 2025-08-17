
# Parcial – Aplicación CLI para Gestión de Artículos de Presupuesto

## 🎯 Objetivo
Desarrollar una **aplicación de línea de comandos en Python 3** que permita **registrar, buscar, editar, eliminar y listar** artículos dentro de un sistema de registro de presupuesto. La app debe ejecutarse desde la terminal y **persistir los datos en almacenamiento local** (SQLite recomendado).

---

## 📝 Contexto
La herramienta permitirá a los usuarios llevar un control detallado de insumos, costos y componentes de un presupuesto. Cada artículo tendrá: **nombre, categoría, cantidad, precio unitario y descripción**. La app debe ser robusta, fácil de usar y con mensajes amigables.

---

## ✅ Requisitos funcionales
La aplicación debe permitir:
1. **Registrar** un artículo nuevo  
   - Campos: `nombre`, `categoría`, `cantidad`, `precio_unitario`, `descripción`.
2. **Buscar** artículos  
   - Por **nombre** y/o **categoría** (coincidencia parcial).
3. **Editar** un artículo existente  
   - Actualizar uno o varios campos.
4. **Eliminar** un artículo  
   - Por **id** o por **nombre** (si es único).
5. **Listar** todos los artículos  
   - Mostrar en formato legible (tabulado/alineado), incluyendo **subtotal = cantidad × precio**.

---

## 💡 Consideraciones técnicas
- Lenguaje: **Python 3**.
- Persistencia: **SQLite** (alternativa: JSON/CSV con lectura/escritura segura).
- Estructura del proyecto **modular** (funciones y/o clases).
- **Validar entradas** (no vacíos, números válidos, no negativos).
- **Manejo de errores** y mensajes al usuario (input inválido, registros inexistentes, etc.).
- **Comentarios** y organización del código (docstrings y comentarios claros).
- El programa debe ejecutarse con `python app.py` o similar.
- Incluir un **archivo README** con: descripción, instalación, uso y ejemplos.
- No usar librerías externas para imprimir tablas (opcional: crear función propia).

---

## 📦 Entregables
1. Código fuente (por ejemplo `budget_cli.py` o estructura equivalente).
2. Archivo de base de datos (si se requiere para demostrar funcionamiento) o instrucciones para generarlo.
3. **README** con instrucciones de ejecución, ejemplos y notas técnicas.
4. (Opcional) Un breve **video/gif** o capturas mostrando el funcionamiento.

---

## 🔧 Sugerencia de interfaz (ejemplo con subcomandos)
> Puedes usar `argparse` y subcomandos como referencia.

```
python budget_cli.py add    --name "Cemento" --category "Materiales" --quantity 20 --price 12.5 --desc "Bolsas 42.5kg"
python budget_cli.py list
python budget_cli.py search --name ceme
python budget_cli.py search --category "obra"
python budget_cli.py edit   --id 1 --quantity 25 --price 13.0
python budget_cli.py delete --id 1
# Base de datos alternativa
python budget_cli.py --db presupuesto.db list
```

---

## 🧪 Criterios de evaluación (rúbrica sugerida)
| Criterio | Puntos |
|---|---|
| Cumplimiento de requisitos funcionales | 40 |
| Persistencia local correcta (SQLite/JSON/CSV) | 15 |
| Validación de entradas y manejo de errores | 15 |
| Calidad del código (modularidad, legibilidad, comentarios) | 15 |
| Interfaz CLI clara y ejemplos en README | 10 |
| Presentación/demostración (capturas o breve video) | 5 |
| **Total** | **100** |

**Penalizaciones comunes**: sin persistencia, sin validación, errores al ejecutar, no incluir README, no seguir especificaciones.

---

## 🧭 Estructura mínima sugerida
```
.
├── budget_cli.py           # App principal (o paquete con módulos)
├── budget.db               # SQLite (generado automáticamente)
├── README.md               # Instrucciones de uso
└── tests/                  # (Opcional) Casos de prueba manuales/automáticos
```

---

## 🏁 Requisitos de ejecución
- Python 3.8+
- No se requieren librerías externas.
- En sistemas Unix, dar permiso de ejecución si se usa shebang: `chmod +x budget_cli.py`

---

## 📚 Recomendaciones
- Implementa una función para **formatear montos** y otra para **imprimir tablas**.
- Maneja `sqlite3` con **context managers** y habilita `PRAGMA foreign_keys = ON`.
- Si usas JSON/CSV, asegura **lectura/escritura atómica** y manejo de archivos inexistentes.
- Evita **duplicar lógica** entre comandos; reutiliza funciones.

---

## 🔒 Reglas del parcial
- Trabajo **individual** (salvo que el docente indique lo contrario).
- Se permite consultar documentación oficial de Python.
- No se permite copiar código de terceros sin citar; se evaluará **originalidad**.

---

## 🧩 Bonus (opcional)
- Comando para **exportar** a CSV.
- Campo calculado **subtotal** y **total general** en `list`.
- Tests unitarios simples con `unittest` o `pytest`.
- Colores o resaltado en CLI (sin dependencias externas).
- Empaquetado en `requirements.txt` (si hicieras extensiones).

---

## 📄 Licencia
Incluye una licencia a tu preferencia (MIT recomendado) o aclara el uso académico.
