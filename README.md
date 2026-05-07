[![DianaLabs - Nuevos](https://img.shields.io/badge/DianaLabs-Nuevos-8A2BE2?logo=github)](https://github.com/dianahuarazvargas-bit/dianalabs-koha-nuevos)
# 📘 Guía de Usuario: Conversión de Google Sheets a MARC21 para Koha

## Conversión de Google Sheets → MARC21 para Koha

Esta guía explica cómo **transformar registros bibliográficos desde una hoja de cálculo** (Google Sheets) al formato **MARC21**, para poder importarlos masivamente al sistema **Koha**.

## 📁 Archivos del repositorio

| Archivo | Descripción |
|---------|-------------|
| [`GoogleSheets_a_Marc_Libros_Nuevos.ipynb`](./GoogleSheets_a_Marc_Libros_Nuevos.ipynb) | Notebook de Google Colab que ejecuta la conversión de Excel/Sheets a MARC21 |
| [`Plantilla_Libros_Nuevos_GSheets.xlsx`](./Plantilla_Libros_Nuevos_GSheets.xlsx) | Plantilla de ejemplo con las columnas y encabezados esperados por el script |
| `README.md` | Esta guía — instrucciones completas para usar el sistema |

---

## 🧭 1. Flujo general del proceso

| Etapa | Acción |
|-------|--------|
| 1. Preparación | Organizar los datos en Excel/Google Sheets según la plantilla institucional |
| 2. Vinculación | Subir los datos a Google Sheets y obtener el enlace de exportación |
| 3. Procesamiento | Ejecutar un script en **Python (Google Colab)** que convierte los datos |
| 4. Importación | Cargar el archivo `.mrc` resultante en el módulo de catalogación de Koha |

---

## 📋 2. Preparación del Google Sheets

Para que el script funcione correctamente, la hoja de cálculo debe cumplir estos **requisitos obligatorios**:

✅ **Encabezados exactos**  
Deben coincidir con los nombres esperados por el script, por ejemplo:  
`titulo_245`, `autor_institucion_110`, `952_a`, etc.

✅ **Datos limpios**  
No debe haber espacios en blanco antes o después de los nombres de las columnas.

✅ **Acceso público o autenticado**  
La hoja debe estar configurada como:  
> *“Cualquier persona con el enlace puede leer”*

> 💡 **Nota:** Si usas autenticación en Colab, asegúrate de tener permisos de acceso.

---

## 🐍 3. Descripción del script de conversión

El script está escrito en **Python** y usa la librería `pymarc`. Estas son sus partes principales:

### A. Instalación y conexión
```python
!pip install pymarc
```
Convierte automáticamente la URL de edición de Google Sheets en una URL de exportación CSV válida para `pandas`.

### B. Validación de datos
Verifica que los campos `isbn_020`, `issn_022` y `dl_024` contengan **solo números y guiones**.  
Si encuentra caracteres no válidos, **muestra una alerta** antes de generar el archivo.

### C. Mapeo de campos MARC21
El script asigna cada columna del Sheets a una etiqueta MARC:

| Etiqueta | Contenido |
|----------|-----------|
| 100 | Autor principal |
| 245 | Título |
| 260 | Publicación |
| 300 | Descripción física |
| 600, 610, 650, 651 | Materias |
| 942$c | Tipo de ítem (Koha) |
| 952 | Ejemplares (ubicación, signatura, copia) |

> 🔁 **Ejemplares múltiples:**  
> Este script **maneja automáticamente ejemplares múltiples**.  
> Por ejemplo, si una fila contiene `numero_copia_952_t.1` y `numero_copia_952_t.2`, el sistema creará **tres ítems** (el original + dos copias) vinculados al mismo registro bibliográfico en Koha, sin necesidad de intervención manual.

---

## ⚙️ 4. Ejecución y descarga del archivo MRC

Sigue estos pasos dentro de **Google Colab**:

1. **Abrir el notebook**  
   `Excel_a_Marc_Libros_Nuevos.ipynb`

2. **Actualizar la URL**  
   Reemplaza esta variable con el enlace de Google Sheets:
   ```python
   google_sheet_url = "TU_ENLACE_AQUÍ"
   ```

3. **Ejecutar todas las celdas**  
   Ve a: `Entorno de ejecución > Ejecutar todas`

4. **Previsualizar resultados**  
   El script mostrará los primeros 5 registros en formato mnemotécnico, algo como:
   ```
   =245 10$aTítulo del libro...
   ```

5. **Descargar el archivo**  
   Se generará automáticamente el archivo:  
   `koha_Libros_from_GoogleSheet.mrc`

---

## 📥 5. Importación en Koha

Una vez que tengas el archivo `.mrc`, sigue estos pasos dentro de Koha:

1. Ve a:  
   `Herramientas > Subir archivos MARC para importación > btn: Seleccionar archivo > btn: Seleccione el archivo a importar > btn: Administrar registros preparados > btn: importar este lote en el catálogo`

2. **Selecciona el archivo** `.mrc` que descargaste

3. **Procesa el archivo** para revisión

4. **Verifica que no haya duplicados**  
   Koha te mostrará coincidencias posibles con registros existentes.

5. **Completa la importación definitiva** al catálogo.

---
