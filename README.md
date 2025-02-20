# Risk-Indicator
## 1. Análisis Descriptivo
## 2. Notebook ushay.ipynb
### 2.1 Procesamiento y Reconstrucción de Archivos .ushay
### Objetivo
Procesar archivos `.ushay` que pueden estar en partes pero son identificados a un proceso de compra pública con un sl_contract_id, luego reconstruirlos en archivos ZIP, garantizando la integridad y evitando archivos corruptos. Se distinguen archivos únicos de aquellos fragmentados (por partes).

## ¿Qué es un archivo .ushay?
Un archivo **.ushay** es el formato que utiliza el SOCE para comprimir archivos, similar a un ZIP, pero que incluye un separador llamado **sercop** para dividir internamente el contenido. Estos archivos contienen documentos del proceso de contratación pública.

## Funcionamiento
1. **Carga y Filtrado:**  
   - Se lee el CSV y se filtran los registros con extensión `.ushay`.  
   - Se ajustan las rutas de los archivos para su correcta ubicación.

2. **Agrupación y Clasificación:**  
   - Se agrupan los archivos por `sl_contract_id`.  
   - **CASO 1:** **Archivo único** -> Se guarda directamente en formato ZIP.  
   - **CASO 2:** **Archivo fragmentado** -> Se unen las partes tras verificar la integridad con SHA-1 y se genera el ZIP.

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
| 123456         | parte1.ushay        | .ushay   | abc12345 | /ruta/parte1.ushay  |
| 123456         | parte2.ushay        | .ushay   | 456def78 | /ruta/parte2.ushay  |
| 789101         | documento_unico.ushay | .ushay   | xyz98765 | /ruta/unico.ushay   |

### Salida
- **123456:** Archivo fragmentado, reconstruido a partir de partes y guardado en ZIP.  
- **789101:** Archivo único guardado directamente en ZIP.
### 2.2 Procesamiento y Visualización de Embeddings de Documentos

Se procesa documentos de texto en formatos PDF, DOCX y TXT, extrae su contenido, genera embeddings utilizando Doc2Vec, y los combina con indicadores de un dataset externo para realizar análisis de correlación y visualización de datos con PCA y t-SNE.

**1. Carga y Preprocesamiento de Archivos**

Se define el directorio donde están los archivos descomprimidos.
Se excluyen ciertos archivos y extensiones no relevantes (imágenes, videos, archivos comprimidos, etc.).
Se extrae texto de archivos:

- TXT: Se lee directamente.
- PDF: Se extrae con pdfplumber.
- DOCX: Se extrae con python-docx.

**2. Generación de Embeddings con Doc2Vec**

- Se entrena un modelo Doc2Vec con los documentos procesados.
- Se generan embeddings para cada documento.
- Se guardan los embeddings en un archivo CSV.

**3. Análisis de Correlación**

- Se calcula la matriz de correlación entre los embeddings y los indicadores.
- Se genera un mapa de calor para visualizar las correlaciones.
  ![output](https://github.com/user-attachments/assets/f9f5bd5c-3162-4b8d-9bce-2902d20ab4ab)

**4. Visualización de Embeddings**

- Se clasifican los valores de sie_ic_promedio en categorías (Bajo, Medio, Medio Alto, Alto).
- Se aplican dos métodos de reducción de dimensionalidad:

   - PCA (Principal Component Analysis)
     <img width="693" alt="Screenshot 2025-02-20 at 1 46 32 PM" src="https://github.com/user-attachments/assets/9e81eb0b-83d3-4e2d-beed-4dba3d76b282" />
   - t-SNE (t-Distributed Stochastic Neighbor Embedding)
      <img width="758" alt="Screenshot 2025-02-20 at 1 46 15 PM" src="https://github.com/user-attachments/assets/65207e79-ce12-4cd1-bca7-1f83248e694b" />


