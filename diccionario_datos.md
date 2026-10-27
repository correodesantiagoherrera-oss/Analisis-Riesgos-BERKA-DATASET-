# Diccionario de datos — Berka Dataset (PKDD'99)

Datos reales y anonimizados de un banco checo, de 1993 a 1998.
Todos los importes están en **coronas checas (CZK)**.
Las fechas vienen como números con formato **AAMMDD** (por ejemplo, `930705` = 5 de julio de 1993).

---

## Cómo se relacionan las tablas

```
district ─── account ─── loan
   │            │
   │            ├─── trans
   │            ├─── order
   │            │
   └─── client ─┴─ disp ─── card
```

- Cada **préstamo** (`loan`) pertenece a una **cuenta** (`account`) → se unen por `account_id`.
- Cada **cuenta** está en un **distrito** (`district`) → se unen por `account.district_id = district.A1`.
- **Clientes** y **cuentas** se unen a través de `disp` (una cuenta puede tener varios clientes).
- Cada **tarjeta** (`card`) pertenece a un registro de `disp` → se unen por `disp_id`.

---

## loan — Préstamos (682 filas)

| Columna | Significado |
|---|---|
| `loan_id` | Identificador del préstamo |
| `account_id` | Cuenta que pidió el préstamo |
| `date` | Fecha de concesión (AAMMDD) |
| `amount` | Importe del préstamo |
| `duration` | Duración en meses |
| `payments` | Cuota mensual |
| `status` | Estado del préstamo (ver abajo) |

**Valores de `status`:**

| Valor | Significado | ¿Bueno o malo? |
|---|---|---|
| A | Contrato terminado, pagado sin problemas | Bueno |
| B | Contrato terminado, préstamo no pagado | Malo |
| C | Contrato en curso, al día hasta ahora | Bueno |
| D | Contrato en curso, cliente con deudas | Malo |

---

## account — Cuentas (4.500 filas)

| Columna | Significado |
|---|---|
| `account_id` | Identificador de la cuenta |
| `district_id` | Distrito de la sucursal de la cuenta |
| `frequency` | Cada cuánto se emiten los extractos (ver abajo) |
| `date` | Fecha de apertura de la cuenta (AAMMDD) |

**Valores de `frequency`:**

| Valor (checo) | Significado |
|---|---|
| POPLATEK MESICNE | Extracto mensual |
| POPLATEK TYDNE | Extracto semanal |
| POPLATEK PO OBRATU | Extracto después de cada transacción |

---

## district — Distritos (77 filas)

Las columnas tienen nombres codificados, de `A1` a `A16`.

| Columna | Significado |
|---|---|
| `A1` | Identificador del distrito (se une con `district_id` de `account` y `client`) |
| `A2` | Nombre del distrito |
| `A3` | Región |
| `A4` | Número de habitantes |
| `A5` | Nº de municipios con menos de 500 habitantes |
| `A6` | Nº de municipios con 500–1.999 habitantes |
| `A7` | Nº de municipios con 2.000–9.999 habitantes |
| `A8` | Nº de municipios con 10.000 habitantes o más |
| `A9` | Número de ciudades |
| `A10` | % de población urbana |
| `A11` | Salario medio |
| `A12` | Tasa de paro en 1995 (%) |
| `A13` | Tasa de paro en 1996 (%) |
| `A14` | Nº de empresarios por cada 1.000 habitantes |
| `A15` | Nº de delitos en 1995 |
| `A16` | Nº de delitos en 1996 |

*Nota: `A12` y `A15` pueden tener algún valor vacío o con `?`.*

---

## client — Clientes (5.369 filas)

| Columna | Significado |
|---|---|
| `client_id` | Identificador del cliente |
| `birth_number` | Fecha de nacimiento y sexo codificados (ver abajo) |
| `district_id` | Distrito donde vive el cliente |

**Cómo leer `birth_number`:** formato AAMMDD.
- Hombres: el mes va normal (01–12).
- Mujeres: al mes se le suma 50 (51–62).
- Ejemplo: `706213` → mujer nacida el 13 de diciembre de 1970.

---

## disp — Relación cliente-cuenta (5.369 filas)

| Columna | Significado |
|---|---|
| `disp_id` | Identificador del registro |
| `client_id` | Cliente |
| `account_id` | Cuenta |
| `type` | `OWNER` (titular) o `DISPONENT` (usuario autorizado) |

Solo el titular (`OWNER`) puede pedir un préstamo o dar órdenes permanentes.

---

## card — Tarjetas (892 filas)

| Columna | Significado |
|---|---|
| `card_id` | Identificador de la tarjeta |
| `disp_id` | Registro de `disp` al que pertenece |
| `type` | Tipo: `junior`, `classic` o `gold` |
| `issued` | Fecha de emisión |

---

## order — Órdenes permanentes (6.471 filas)

*En la base de datos del proyecto esta tabla se llama `orders`, porque `ORDER` es una palabra reservada de SQL.*

| Columna | Significado |
|---|---|
| `order_id` | Identificador de la orden |
| `account_id` | Cuenta desde la que se paga |
| `bank_to` | Banco del destinatario |
| `account_to` | Cuenta del destinatario |
| `amount` | Importe |
| `k_symbol` | Tipo de pago (ver abajo) |

**Valores de `k_symbol`:**

| Valor (checo) | Significado |
|---|---|
| POJISTNE | Pago de seguro |
| SIPO | Pago de gastos del hogar |
| LEASING | Leasing |
| UVER | Pago de un préstamo |

---

## trans — Transacciones (~1.056.000 filas)

| Columna | Significado |
|---|---|
| `trans_id` | Identificador de la transacción |
| `account_id` | Cuenta |
| `date` | Fecha (AAMMDD) |
| `type` | Ingreso o salida de dinero (ver abajo) |
| `operation` | Tipo de operación (ver abajo) |
| `amount` | Importe |
| `balance` | Saldo de la cuenta después de la transacción |
| `k_symbol` | Concepto (ver abajo) |
| `bank` | Banco de la otra parte |
| `account` | Cuenta de la otra parte |

**Valores de `type`:**

| Valor (checo) | Significado |
|---|---|
| PRIJEM | Ingreso |
| VYDAJ | Salida de dinero |
| VYBER | Retirada en efectivo |

**Valores de `operation`:**

| Valor (checo) | Significado |
|---|---|
| VYBER KARTOU | Retirada con tarjeta |
| VKLAD | Ingreso en efectivo |
| PREVOD Z UCTU | Transferencia recibida de otro banco |
| VYBER | Retirada en efectivo |
| PREVOD NA UCET | Transferencia enviada a otro banco |

**Valores de `k_symbol`:**

| Valor (checo) | Significado |
|---|---|
| POJISTNE | Pago de seguro |
| SLUZBY | Comisión por extracto |
| UROK | Intereses a favor |
| SANKC. UROK | Intereses de penalización por saldo negativo |
| SIPO | Pago de gastos del hogar |
| DUCHOD | Pensión |
| UVER | Pago de un préstamo |

---

*Fuente: documentación original del PKDD'99 Discovery Challenge (Petr Berka).*
