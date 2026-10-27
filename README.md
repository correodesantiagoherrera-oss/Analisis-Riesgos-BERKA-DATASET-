# Análisis de riesgo de préstamos — Banco checo

Análisis de los préstamos de un banco real con **SQL** y **Python** para responder a una pregunta: **¿qué características tienen los préstamos que salen mal?**

Uso SQL para unir, filtrar y resumir los datos, y Python (pandas y matplotlib) para calcular porcentajes y hacer los gráficos.

---

## Preguntas del análisis

1. ¿Cuántos préstamos salen bien y cuántos salen mal?
2. ¿Influye el importe del préstamo en que salga mal?
3. ¿Hay regiones donde los préstamos fallen más?

---

## Datos

**Dataset Berka (PKDD'99):** datos reales y anonimizados de un banco checo entre 1993 y 1998. Los importes están en coronas checas.

Son 8 tablas relacionadas (préstamos, cuentas, clientes, distritos, transacciones, tarjetas...). En este análisis uso tres:

| Tabla | Contenido |
|---|---|
| `loan` | 682 préstamos: importe, duración, cuota y estado |
| `account` | 4.500 cuentas: sirve para unir cada préstamo con su distrito |
| `district` | 77 distritos: nombre, región y datos socioeconómicos |

**Estados del préstamo:**

| status | Significado | Tipo |
|---|---|---|
| A | Terminado y pagado | Bueno |
| B | Terminado y sin pagar | Malo |
| C | En curso y al día | Bueno |
| D | En curso con deudas | Malo |

El significado de todas las columnas está en el [diccionario de datos](diccionario_datos.md).

**Dónde conseguir los datos:** en [Kaggle](https://www.kaggle.com) (buscando "Berka dataset") o en la fuente original, el [CTU Relational Dataset Repository](https://relational.fel.cvut.cz/dataset/Financial).

---

## Herramientas

- **SQL** (SQLite): consultas, `JOIN` entre tablas, `GROUP BY`, `CASE WHEN`
- **Python**: pandas para cargar los datos y calcular, matplotlib para los gráficos
- **Jupyter Notebook**

---

## Resultados principales

**1. El 11,1 % de los préstamos sale mal, pero pesan el 15,1 % del dinero prestado.**
De 682 préstamos, 76 son malos (B o D). Como suelen ser préstamos grandes, el problema pesa más en dinero que en número.

**2. Los préstamos que salen mal son más grandes.**
Comparando préstamos en la misma situación:
- Terminados: los impagados (B) son de media un **54 % más grandes** que los pagados (A).
- En curso: los que tienen deudas (D) son un **45 % más grandes** que los que van al día (C).

En los préstamos de más de 400.000 coronas, la tasa de impago llega al **26,9 %**, más del doble que la media.

**3. La región importa.**

| Región | Tasa de impago |
|---|---|
| west Bohemia | 15,8 % |
| north Moravia | 15,4 % |
| south Bohemia | 15,0 % |
| central Bohemia | 11,1 % |
| east Bohemia | 10,7 % |
| south Moravia | 10,1 % |
| Prague | 8,3 % |
| north Bohemia | 1,6 % |

Tres regiones están alrededor del 15 %, casi el doble que Praga (8,3 %). El banco podría ser más exigente al conceder préstamos en ellas.

---

## Limitaciones

- Son pocos préstamos (682), así que algunos resultados pueden deberse en parte a la casualidad. Por ejemplo, el 1,6 % de north Bohemia se basa en un solo impagado de 61 préstamos.
- Muchos préstamos siguen en curso (C y D), así que todavía no se sabe cómo terminarán.

---

## Próximos pasos

- Comprobar si el salario medio o el paro de cada distrito explican las diferencias entre regiones.
- Convertir las fechas (formato AAMMDD) para analizar la evolución en el tiempo.
- Construir un modelo sencillo que prediga qué préstamos tienen más riesgo de impago.
- Migrar la base de datos a PostgreSQL.

---

## Cómo ejecutarlo

1. Clona el repositorio.
2. Descarga los 8 archivos CSV del dataset y ponlos en una carpeta `data/` dentro del proyecto.
3. Instala las librerías:
   ```
   pip install pandas matplotlib jupyter
   ```
4. Abre el notebook y ejecuta las celdas en orden. La primera vez se crea la base de datos `banco.db`.

---

## Estructura del proyecto

```
proyecto-banco/
├── data/                  # CSV del dataset (no incluidos en el repositorio)
├── Berka Notebook.ipynb   # Análisis completo
├── diccionario_datos.md   # Significado de cada tabla y columna
└── README.md
```
