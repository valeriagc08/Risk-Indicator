# Risk-Indicator

# Procesamiento y Reconstrucción de Archivos .ushay

## Objetivo
Procesar archivos `.ushay` a partir de un CSV y reconstruirlos en archivos ZIP, garantizando la integridad y evitando archivos corruptos. Se distinguen archivos únicos de aquellos fragmentados.

## ¿Qué es un archivo .ushay?
Un archivo **.ushay** es el formato que utiliza el SOCE para comprimir archivos, similar a un ZIP, pero que incluye un separador llamado **sercop** para dividir internamente el contenido. Estos archivos contienen documentos del proceso de contratación pública.

## Funcionamiento
1. **Carga y Filtrado:**  
   - Se lee el CSV y se filtran los registros con extensión `.ushay`.  
   - Se ajustan las rutas de los archivos para su correcta ubicación.

2. **Agrupación y Clasificación:**  
   - Se agrupan los archivos por `sl_contract_id`.  
   - **Archivo único:** Se guarda directamente en formato ZIP.  
   - **Archivo fragmentado:** Se unen las partes tras verificar la integridad con SHA-1 y se genera el ZIP consolidado.

3. **Verificación y Almacenamiento:**  
   - Se utiliza SHA-1 para validar la integridad de los fragmentos y se manejan errores para evitar el procesamiento de archivos corruptos.

## Funciones Clave

| Función                                           | Descripción                                                                 |
|---------------------------------------------------|-----------------------------------------------------------------------------|
| `calcular_hash_sha1(contenido)`                   | Calcula el hash SHA-1 de un contenido binario para verificar su integridad. |
| `verificar_y_guardar_archivo(archivo, destino_zip)` | Guarda un archivo único en formato ZIP.                                    |
| `verificar_y_unir_partes(partes, destino_zip)`    | Une archivos fragmentados y genera un ZIP consolidado.                     |
| `modificar_ruta(ruta)`                            | Ajusta las rutas, reemplazando `"ushay_handling"` por `"ushay_handling-2"`.   |

## Ejemplo de Entrada y Salida

### Entrada (CSV)
| sl_contract_id | filename              | extension | hash     | file_path           |
|----------------|-----------------------|-----------|----------|---------------------|
| 123456         | contrato.ushay        | .ushay   | abc12345 | /ruta/parte1.ushay  |
| 123456         | contrato.ushay        | .ushay   | 456def78 | /ruta/parte2.ushay  |
| 789101         | documento_unico.ushay | .ushay   | xyz98765 | /ruta/unico.ushay   |

### Salida
- **123456:** Archivo fragmentado, reconstruido a partir de partes y guardado en ZIP.  
- **789101:** Archivo único guardado directamente en ZIP.
