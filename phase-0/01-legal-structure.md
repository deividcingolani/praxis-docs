# Phase 0.1: Estructura Legal y Jurisdiccion

> **DISCLAIMER:** Este documento es un analisis preliminar y no constituye asesoria legal formal. Se recomienda consultar con abogados especializados en cada jurisdiccion antes de tomar decisiones corporativas, regulatorias o de cumplimiento. Las regulaciones en el espacio de activos digitales y mercados predictivos evolucionan rapidamente; toda la informacion aqui contenida debe ser verificada con counsel local actualizado.

---

## Tabla de Contenidos

1. [Analisis Jurisdiccional Comparativo](#1-analisis-jurisdiccional-comparativo)
2. [Recomendacion de Estructura Corporativa](#2-recomendacion-de-estructura-corporativa)
3. [Matriz de Riesgo Regulatorio](#3-matriz-de-riesgo-regulatorio)
4. [Framework de Terminos de Servicio](#4-framework-de-terminos-de-servicio)
5. [Politica de Privacidad](#5-politica-de-privacidad)
6. [Requisitos de Geo-blocking](#6-requisitos-de-geo-blocking)
7. [Estructura de KYC por Tiers](#7-estructura-de-kyc-por-tiers)
8. [AML/CFT Framework](#8-amlcft-framework)

---

## 1. Analisis Jurisdiccional Comparativo

### 1.1 Tabla Comparativa Principal

| Criterio | Panama | BVI (British Virgin Islands) | Malta | Curacao | Cayman Islands |
|---|---|---|---|---|---|
| **Tipo de licencia requerida** | No existe licencia especifica para prediction markets. Se opera bajo regimen general de sociedad anonima (S.A.) o fundacion de interes privado. No hay regulador cripto dedicado (al 2026, proyecto de ley en discusion). | No requiere licencia especifica para prediction markets. BVI Financial Services Commission (FSC) regula servicios financieros. Se puede operar bajo BVI Business Company con actividad offshore. | Malta Gaming Authority (MGA) o Malta Financial Services Authority (MFSA) bajo Virtual Financial Assets Act (VFA). Prediction markets pueden caer bajo MGA si se clasifican como "games of skill" o bajo MFSA si se tratan como instrumentos financieros. | Licencia de juego en linea (e-Gaming) emitida por Curacao Gaming Control Board (GCB). Regimen historicamente laxo, en transicion hacia mayor regulacion desde 2023-2024. | No licencia especifica. Cayman Islands Monetary Authority (CIMA) regula fondos y servicios financieros. Virtual Asset Service Provider (VASP) registration requerido desde 2022. |
| **Costo estimado de setup** | $5,000 - $15,000 (incorporacion + agente registrado). Fundacion: $8,000 - $20,000. | $3,000 - $10,000 (incorporacion). Fees anuales ~$1,500. Si requiere FSC licensing: $50,000+. | MGA: EUR 25,000 - 50,000 (application fee) + EUR 100,000+ en setup. MFSA VFA: EUR 50,000+ application + capital requirements. Total con asesores: EUR 200,000 - 500,000. | $20,000 - $40,000 (licencia e-Gaming legacy). Nuevo regimen GCB: estimado $30,000 - $60,000+. | $10,000 - $25,000 (incorporacion). VASP registration: $5,000 - $15,000. Si se requiere licencia CIMA completa: $50,000+. |
| **Tiempo de obtencion** | 2-4 semanas (incorporacion). No hay proceso de licencia dedicado. | 1-2 semanas (incorporacion). FSC licensing: 3-6 meses. | MGA: 6-12 meses. MFSA VFA: 6-18 meses. Proceso riguroso con auditorias. | Legacy: 4-8 semanas. Nuevo regimen: 3-6 meses estimado. | 2-4 semanas (incorporacion). VASP: 2-4 meses. CIMA full: 6-12 meses. |
| **Impuestos corporativos** | 0% sobre ingresos de fuente extranjera (sistema territorial). Solo tributan ingresos generados dentro de Panama. | 0% impuesto corporativo. Sin impuesto a ganancias de capital, withholding taxes, ni IVA. | 35% tasa nominal, reducible a 5% efectivo mediante sistema de refund para accionistas no residentes. Incentivos para empresas tech/gaming. | 2% impuesto sobre ganancias (Gaming Tax) + 0% corporativo sobre ingresos offshore. | 0% impuesto corporativo. Sin impuesto a ganancias, dividendos, o capital gains. Certificado de exencion fiscal por 20-25 anos. |
| **Proteccion legal de directores/fundadores** | Fundacion de interes privado ofrece separacion patrimonial fuerte. Leyes de privacidad robustas. No hay tratado de extradicion con muchos paises (pero si con EE.UU.). | Fuerte proteccion de privacidad. Informacion de beneficiarios finales no publica (pero accesible a autoridades). Limited liability robusto. | Regimen regulatorio claro brinda certeza juridica. Proteccion bajo framework EU. Responsabilidad limitada estandar. | Proteccion limitada. Jurisdiccion en transicion regulatoria genera incertidumbre. Parte del Reino de los Paises Bajos (potencial aplicacion indirecta de normativa EU). | Excelente proteccion de activos. Exempted companies ofrecen fuerte privacidad. Regimen legal basado en common law ingles bien desarrollado. |
| **Requisitos de sustancia local** | Minimos. Requiere agente registrado y direccion legal en Panama. No obligatorio tener empleados locales para sociedad offshore, aunque recomendable para fundaciones. | Requiere agente registrado y oficina registrada en BVI. Desde 2019, Economic Substance Act exige sustancia para "relevant activities" (puede incluir servicios financieros). | Sustancia significativa requerida. Directores locales, oficina fisica, compliance officer, MLRO (Money Laundering Reporting Officer), key function holders residentes en Malta/EU. | Requiere oficina local y representante legal (curacao-based entity). Director local recomendado. Sustancia minima pero en aumento. | Requiere oficina registrada. VASP: al menos dos directores (uno puede ser corporativo). Sustancia moderada, en aumento para VASP. |
| **Ventajas** | Neutralidad fiscal offshore. Fundaciones de interes privado como wrapper ideal para DAOs. Privacidad fuerte. Bajo costo. Sin regulacion cripto especifica = flexibilidad. Zona horaria compatible con Americas. | Reconocimiento internacional. Cero impuestos. Privacidad. Rapidez de incorporacion. Jurisdiccion probada para fondos cripto. Marco legal maduro de common law. | Unica jurisdiccion con framework regulatorio integral para cripto/gaming en la EU. Licencia MGA da acceso a mercado europeo. Reputacion regulatoria seria. Pasaporte EU. | Costo relativamente bajo. Proceso rapido. Historico de albergar plataformas de gambling/gaming online. Conectividad con Europa via Paises Bajos. | Cero impuestos garantizado. Excelente reputacion financiera. Marco legal sofisticado. Industria de fondos cripto madura. Estabilidad politica. |
| **Desventajas** | Percepcion reputacional negativa (lista gris GAFI historica). Sin framework regulatorio claro = inseguridad juridica. Relacion compleja con EE.UU. (Canal, tratados). Dificultad para obtener banking. | Economic Substance Act genera costos adicionales. Percepcion de "tax haven" puede dificultar relaciones bancarias. Sin framework cripto especifico. Dependencia del UK post-Brexit. | Costo alto. Proceso lento y burocratico. Requisitos de sustancia pesados. Supervision continua intensa. Puede no ser ideal si se quiere evitar regulacion EU (MiCA). | Incertidumbre regulatoria por transicion. Reputacion cuestionable en la industria. Infrastructure tecnologica limitada. Dependencia de la politica de Paises Bajos. Acceso bancario dificil. | Costo de vida y operacion muy alto. No es miembro de la EU (sin pasaporte). Regulacion VASP en evolucion. Presion internacional por transparencia fiscal creciente. |

### 1.2 Resumen Ejecutivo Jurisdiccional

**Para una plataforma de prediction markets con componente cripto/blockchain:**

- **Mejor balance costo/flexibilidad:** Panama (holding) + BVI (operativa) o Panama sola con fundacion.
- **Mejor para acceso al mercado europeo:** Malta (pero costo y tiempo significativos).
- **Mejor para neutralidad fiscal pura:** Cayman Islands o BVI.
- **Menos recomendada:** Curacao (incertidumbre regulatoria, reputacion en deterioro).

---

## 2. Recomendacion de Estructura Corporativa

### 2.1 Estructura Propuesta: Entidad Operativa + Fundacion/DAO Wrapper

Se recomienda una estructura de dos capas que separe la gobernanza descentralizada de la operacion comercial:

```
                    +----------------------------------+
                    |  FUNDACION DE INTERES PRIVADO    |
                    |  (Panama)                         |
                    |                                   |
                    |  - Titular del protocolo/IP       |
                    |  - Gobernanza via DAO council     |
                    |  - Sin fines de lucro             |
                    |  - Beneficiarios: el ecosistema   |
                    +----------------+-----------------+
                                     |
                                     | 100% ownership
                                     |
                    +----------------v-----------------+
                    |  ENTIDAD OPERATIVA (OpCo)         |
                    |  (BVI Business Company o          |
                    |   Panama S.A.)                     |
                    |                                   |
                    |  - Opera la plataforma            |
                    |  - Contrata equipo                |
                    |  - Revenue collection             |
                    |  - Compliance & KYC/AML           |
                    +----------------+-----------------+
                                     |
                         +-----------+-----------+
                         |                       |
                +--------v-------+     +---------v--------+
                |  SUBSIDIARIA   |     |  SUBSIDIARIA     |
                |  TECH/DEV      |     |  MARKETING       |
                |  (Argentina,   |     |  (Dubai/          |
                |   Portugal,    |     |   Singapore)      |
                |   o remoto)    |     |                   |
                +---------+------+     +------------------+
```

### 2.2 Por que esta estructura

**Fundacion de Interes Privado (Panama):**

- **Separacion patrimonial:** La fundacion no tiene "duenos" en el sentido tradicional. Tiene un consejo fundacional y beneficiarios, lo que reduce el riesgo personal de los fundadores.
- **DAO Wrapper natural:** Panama permite que las fundaciones sean gobernadas por reglas internas flexibles, incluyendo mecanismos on-chain para votacion y gobernanza.
- **Propiedad intelectual:** La fundacion es titular del protocolo, contratos inteligentes, y marca. Esto protege el IP de litigios contra la entidad operativa.
- **Sin fines de lucro:** Esto alinea la narrativa regulatoria con "infraestructura publica" en lugar de "casino online".
- **Precedente:** Proyectos como Ethereum Foundation (Suiza), Cardano Foundation, y multiples DAOs han usado estructuras similares.

**Entidad Operativa (OpCo):**

- **Responsabilidad operativa:** Maneja las operaciones diarias, contratacion, relaciones bancarias, y compliance.
- **Revenue collection:** Todos los fees de la plataforma fluyen a traves de la OpCo.
- **Liability shield:** Como subsidiary de la fundacion, los problemas legales de la OpCo no escalan automaticamente a la fundacion.
- **Flexibilidad jurisdiccional:** La OpCo puede ser en BVI (cero impuestos) o Panama (si se prefiere consolidar). BVI es preferible si se busca maxima neutralidad fiscal.

**Subsidiarias funcionales:**

- **Tech/Dev:** Donde reside el equipo de desarrollo. Argentina ofrece talento tech a costo competitivo y regimen de exportacion de servicios favorable. Portugal tiene visa para nomadas digitales y regimen NHR.
- **Marketing/BD:** Dubai o Singapore para presencia comercial en mercados clave. Dubai tiene regimen VASP claro (VARA) y 0% income tax para personas fisicas.

### 2.3 Como implementar

**Paso 1 - Constituir la Fundacion (Semanas 1-4):**
- Contratar abogado corporativo en Panama (firmas recomendadas: Arias, Fabrega & Fabrega; Morgan & Morgan; Patton Moreno & Asvat).
- Definir el acta fundacional con reglas de gobernanza que referencien mecanismos on-chain.
- Designar consejo fundacional inicial (3 miembros minimo recomendado).
- Registrar propiedad intelectual a nombre de la fundacion.

**Paso 2 - Incorporar la OpCo (Semanas 2-6):**
- Si BVI: contratar agente registrado (Harneys, Conyers, Carey Olsen).
- Si Panama: puede hacerse en paralelo con la fundacion.
- Establecer cuenta bancaria corporativa (considerar bancos en Panama, Singapore, o neobancos cripto-friendly como Mercury, Relay, o banking-as-a-service via Bridge o similares).
- Registrar la OpCo como subsidiary 100% de la fundacion.

**Paso 3 - Estructura de gobernanza DAO (Semanas 4-12):**
- Desplegar contratos de gobernanza on-chain (Governor contract, Timelock, Token de gobernanza si aplica).
- Vincular las decisiones del DAO con el consejo fundacional mediante un Operating Agreement o Reglamento Interno.
- Definir que decisiones requieren voto on-chain vs. decisiones operativas delegadas a la OpCo.

**Paso 4 - Subsidiarias funcionales (Semanas 8-16):**
- Incorporar segun necesidad operativa.
- Argentina: S.A.S. (Sociedad por Acciones Simplificada) para contratar developers.
- Dubai: Free Zone company (DMCC o DIFC) para BD y marketing.

### 2.4 Costos Estimados de Estructura Completa

| Componente | Costo Estimado (USD) |
|---|---|
| Fundacion Panama (setup + primer ano) | $15,000 - $25,000 |
| OpCo BVI (setup + primer ano) | $8,000 - $15,000 |
| Legal counsel (estructuracion) | $30,000 - $60,000 |
| Subsidiaria tech (Argentina SAS) | $3,000 - $5,000 |
| Subsidiaria comercial (Dubai FZ) | $15,000 - $25,000 |
| Banking setup & compliance inicial | $10,000 - $20,000 |
| **TOTAL ESTIMADO** | **$81,000 - $150,000** |

---

## 3. Matriz de Riesgo Regulatorio

### 3.1 Analisis por Regulador/Jurisdiccion

#### 3.1.1 CFTC (Commodity Futures Trading Commission) - Estados Unidos

| Aspecto | Detalle |
|---|---|
| **Riesgo** | CRITICO - Nivel 5/5 |
| **Clasificacion** | La CFTC ha clasificado ciertos prediction markets como "event contracts" y "swaps" bajo el Commodity Exchange Act (CEA). En 2024, la CFTC aprobo a Kalshi para operar election markets, pero mantuvo postura restrictiva contra plataformas no registradas. |
| **Precedente clave** | CFTC v. Polymarket (2022): acuerdo de $1.4M por operar "event contracts" sin registro. Polymarket se vio obligada a bloquear usuarios de EE.UU. |
| **Riesgo especifico** | Operar con usuarios de EE.UU. sin ser un Designated Contract Market (DCM) o Swap Execution Facility (SEF) registrado constituye violacion del CEA. Penalidades incluyen multas millonarias y potencial accion penal. |
| **Estrategia de mitigacion** | (1) Geo-bloquear EE.UU. de forma agresiva (IP blocking + VPN detection + phone number verification). (2) Terms of Service explicitamente prohibiendo usuarios de EE.UU. (3) No tener ningun nexo operativo con EE.UU. (servidores, empleados, cuentas bancarias, incorporacion). (4) No aceptar USD fiat directamente. (5) Documentar diligencia debida en exclusion de usuarios americanos. |

#### 3.1.2 MiCA (Markets in Crypto-Assets Regulation) - Union Europea

| Aspecto | Detalle |
|---|---|
| **Riesgo** | ALTO - Nivel 4/5 |
| **Clasificacion** | MiCA (vigente desde junio 2024, implementacion plena enero 2025) regula crypto-asset service providers (CASPs). Prediction markets basados en cripto pueden caer bajo la definicion de "crypto-asset services" si involucran la emision o trading de tokens. |
| **Riesgo especifico** | Si los shares/positions en los mercados se tokenizan, podrian clasificarse como crypto-assets bajo MiCA. Operar como CASP sin autorizacion en la EU conlleva sanciones significativas. Ademas, si los tokens se clasifican como "financial instruments" quedan fuera de MiCA y caen bajo MiFID II, con requisitos aun mas estrictos. |
| **Estrategia de mitigacion** | (1) Si se opera desde Malta con licencia, cumplir MiCA como CASP. (2) Si se opera offshore, evaluar si se esta "soliciting" activamente a usuarios EU (marketing, idiomas, pagos en EUR). (3) Mantener los prediction market shares como utility tokens o mecanismos internos de la plataforma, evitando que se listen en exchanges externos. (4) Considerar geo-blocking de EU si no se obtiene licencia. |

#### 3.1.3 FCA (Financial Conduct Authority) - Reino Unido

| Aspecto | Detalle |
|---|---|
| **Riesgo** | ALTO - Nivel 4/5 |
| **Clasificacion** | La FCA ha clasificado cripto-derivados como instrumentos regulados. Desde 2021, prohibio la venta de cripto-derivados a retail consumers. Prediction markets con settlement en cripto podrian caer bajo esta prohibicion. |
| **Riesgo especifico** | Vender "crypto-derivatives" (que incluye binary options sobre eventos) a consumidores UK sin autorizacion FCA es una violacion penal. La FCA tiene alcance extraterritorial para actividades dirigidas a consumidores UK. |
| **Estrategia de mitigacion** | (1) Geo-bloquear UK o al menos restringir acceso a usuarios UK verificados con KYC avanzado. (2) No hacer marketing dirigido a UK. (3) No denominar mercados en GBP. (4) Estructurar los prediction markets como "non-derivative" instruments (e.g., no binary options, sino "information markets"). |

#### 3.1.4 CNV (Comision Nacional de Valores) - Argentina

| Aspecto | Detalle |
|---|---|
| **Riesgo** | MODERADO - Nivel 3/5 |
| **Clasificacion** | La CNV regula valores negociables. Cripto-activos no tienen regulacion integral en Argentina (al 2026). El BCRA (Banco Central) ha emitido comunicaciones restrictivas sobre servicios cripto pero sin prohibicion total. Ley de Mercados de Capitales podria aplicarse si se consideran los shares como "valores negociables". |
| **Riesgo especifico** | Si la subsidiaria tech esta en Argentina y se considera que opera un mercado financiero no registrado, hay riesgo de accion de la CNV. Ademas, riesgo impositivo (AFIP) si no se estructura correctamente la exportacion de servicios. |
| **Estrategia de mitigacion** | (1) La subsidiaria argentina debe limitarse a servicios de desarrollo de software (exportacion de servicios), no a operacion del mercado. (2) Facturar servicios de desarrollo de la SAS argentina a la OpCo BVI/Panama. (3) No dirigir la plataforma al mercado argentino ni aceptar ARS. (4) Cumplir con regimen de exportacion de servicios (exencion de IVA en exportacion). |

#### 3.1.5 Regulaciones LATAM (Brasil, Mexico, Colombia, Chile)

| Pais | Riesgo | Detalle | Mitigacion |
|---|---|---|---|
| **Brasil** | ALTO (4/5) | CVM (equivalente a SEC) activa en regulacion cripto. Marco regulatorio cripto aprobado (Ley 14.478/2022). Banco Central como regulador de VASPs. Prediction markets podrian caer bajo regulacion de derivados o juegos de azar (regulados a nivel federal). | Geo-blocking recomendado hasta obtener clarity legal. No marketing en portugues brasileno. |
| **Mexico** | MODERADO (3/5) | Ley Fintech (2018) regula Instituciones de Tecnologia Financiera. CNBV supervisa. Prediction markets no regulados especificamente pero podrian caer bajo derivados. | Evitar nexo operativo en Mexico. No aceptar MXN fiat. Monitorear evolucion regulatoria. |
| **Colombia** | BAJO (2/5) | Superfinanciera no ha regulado prediction markets especificamente. Cripto-activos no regulados de forma integral. Enfoque regulatorio reactivo. | Bajo riesgo actual pero mantener monitoreo. |
| **Chile** | MODERADO (3/5) | Ley Fintech (2023) en implementacion. CMF (Comision para el Mercado Financiero) regulara VASPs. Prediction markets podrian caer bajo servicios cripto regulados. | Similar a Mexico: evitar nexo directo, monitorear. |

### 3.2 Matriz Resumen de Riesgo

| Jurisdiccion | Nivel de Riesgo | Accion Recomendada |
|---|---|---|
| EE.UU. (CFTC/SEC) | CRITICO (5/5) | Geo-blocking total. Zero nexo. |
| Union Europea (MiCA) | ALTO (4/5) | Licencia en Malta O geo-blocking. |
| Reino Unido (FCA) | ALTO (4/5) | Geo-blocking o restriccion severa. |
| Brasil (CVM/BCB) | ALTO (4/5) | Geo-blocking recomendado. |
| Argentina (CNV) | MODERADO (3/5) | Solo subsidiary tech, no operacion. |
| Mexico (CNBV) | MODERADO (3/5) | Monitoreo activo, sin nexo. |
| Chile (CMF) | MODERADO (3/5) | Monitoreo activo. |
| Colombia | BAJO (2/5) | Monitoreo pasivo. |
| Paises sanctioned | CRITICO (5/5) | Geo-blocking total obligatorio. |

---

## 4. Framework de Terminos de Servicio

### 4.1 Estructura General del Documento ToS

Los Terminos de Servicio deben organizarse en las siguientes secciones:

```
TERMINOS DE SERVICIO / TERMS OF SERVICE

Fecha de vigencia: [FECHA]
Ultima actualizacion: [FECHA]
Entidad: [Nombre de la OpCo], registrada en [jurisdiccion]

TABLA DE CONTENIDOS:
1.  Definiciones
2.  Aceptacion de los Terminos
3.  Elegibilidad y Restricciones
4.  Descripcion del Servicio
5.  Naturaleza de los Mercados (Disclaimers clave)
6.  Registro de Cuenta y KYC
7.  Depositos, Retiros y Fees
8.  Conducta del Usuario
9.  Propiedad Intelectual
10. Resolucion de Mercados y Disputas
11. Limitacion de Responsabilidad
12. Indemnizacion
13. Ley Aplicable y Jurisdiccion
14. Arbitraje
15. Modificaciones a los Terminos
16. Terminacion de Cuenta
17. Disposiciones Generales
```

### 4.2 Clausulas Clave y Contenido Esencial

#### 4.2.1 Disclaimer: No es Gambling

```
IMPORTANTE: Los servicios proporcionados por la Plataforma constituyen un
mercado de informacion ("information market") y herramienta de agregacion
de conocimiento colectivo. La Plataforma NO es un sitio de apuestas, casino,
casa de juego, ni servicio de gambling en ninguna forma. Los Mercados
ofrecidos en la Plataforma estan disenados como mecanismos de descubrimiento
de precios y agregacion de probabilidades basados en eventos del mundo real.

La participacion en los Mercados requiere analisis, investigacion y
evaluacion informada de probabilidades, y no depende del azar. Los
participantes actuan como "traders" en un mercado de informacion, no como
"apostadores".
```

**Fundamento legal:** Esta distincion es critica porque la mayoria de las jurisdicciones regulan el gambling de manera distinta a los mercados financieros o de informacion. Al posicionar la plataforma como un "information market", se evita la aplicacion de regulaciones de juegos de azar y se alinea con precedentes academicos (Robin Hanson, prediction market theory) que distinguen estos mercados del gambling.

#### 4.2.2 Disclaimer: No son Securities

```
Los shares, posiciones, o tokens utilizados dentro de la Plataforma NO
constituyen valores negociables ("securities"), instrumentos financieros,
derivados, futuros, opciones, swaps, ni ningun otro producto financiero
regulado bajo las leyes de ninguna jurisdiccion.

Los shares representan unicamente una posicion binaria sobre el resultado
de un evento futuro verificable, y funcionan como un mecanismo interno de
la Plataforma. No representan propiedad sobre ninguna empresa, activo
subyacente, flujo de dividendos, ni otorgan derechos de voto corporativo.

La Plataforma no esta registrada como bolsa de valores, mercado de
derivados, agente de bolsa, corredor, ni ningun otro intermediario
financiero regulado en ninguna jurisdiccion.
```

#### 4.2.3 Asuncion de Riesgo

```
ASUNCION DE RIESGO: Al utilizar la Plataforma, usted reconoce y acepta
que:

(a) Participa voluntariamente y bajo su propio riesgo;
(b) Puede perder la totalidad de los fondos depositados o comprometidos
    en cualquier Mercado;
(c) Los resultados de los Mercados dependen de eventos futuros inciertos
    y la Plataforma no garantiza ningun resultado;
(d) La tecnologia blockchain y los contratos inteligentes pueden contener
    errores, vulnerabilidades, o fallar de maneras imprevistas;
(e) El valor de los cripto-activos es inherentemente volatil;
(f) Cambios regulatorios pueden afectar la disponibilidad del servicio
    en su jurisdiccion sin previo aviso;
(g) La resolucion de Mercados se basa en fuentes de informacion externas
    ("oracles") que pueden ser inexactas o estar sujetas a manipulacion;
(h) Usted es el unico responsable de determinar si la participacion en
    la Plataforma es legal en su jurisdiccion de residencia;
(i) Usted es el unico responsable de cumplir con sus obligaciones
    fiscales derivadas de su participacion en la Plataforma.
```

#### 4.2.4 Jurisdiccion Aplicable

```
LEY APLICABLE: Estos Terminos se regiran e interpretaran de acuerdo con
las leyes de [Panama / BVI], sin referencia a sus normas sobre conflictos
de leyes.

Cualquier disputa, controversia o reclamo que surja de o en relacion con
estos Terminos, incluyendo su validez, interpretacion, ejecucion,
incumplimiento o terminacion, sera resuelta exclusivamente mediante
arbitraje de acuerdo con la Seccion [X] (Arbitraje) de estos Terminos.

Los tribunales de [Ciudad de Panama, Panama / Road Town, Tortola, BVI]
tendran jurisdiccion exclusiva unicamente para: (a) ejecucion de laudos
arbitrales; (b) medidas cautelares urgentes previas al arbitraje.
```

#### 4.2.5 Clausula de Arbitraje

```
ARBITRAJE OBLIGATORIO:

(a) Toda disputa sera resuelta mediante arbitraje vinculante administrado
    por [ICC / LCIA / SIAC / CIAC Panama], bajo su reglamento vigente.

(b) El arbitraje sera conducido por un (1) arbitro unico designado de
    acuerdo con el reglamento aplicable.

(c) El idioma del arbitraje sera [ingles/espanol].

(d) La sede del arbitraje sera [Ciudad de Panama / Singapore / London].

(e) El laudo arbitral sera definitivo y vinculante para las partes.

(f) RENUNCIA A ACCIONES COLECTIVAS: Usted acepta que cualquier reclamo
    contra la Plataforma sera presentado unicamente a titulo individual.
    Usted renuncia a su derecho de participar en acciones colectivas
    ("class actions"), demandas representativas, o cualquier procedimiento
    consolidado.

(g) Excepcion para reclamos menores: Disputas por montos inferiores a
    USD $10,000 podran resolverse mediante el mecanismo interno de
    resolucion de disputas de la Plataforma antes de recurrir a arbitraje.
```

#### 4.2.6 Restricciones Geograficas

```
RESTRICCIONES DE ELEGIBILIDAD GEOGRAFICA:

La Plataforma NO esta disponible para personas residentes en, ubicadas en,
incorporadas en, o ciudadanos de las siguientes jurisdicciones ("Regiones
Restringidas"):

- Estados Unidos de America y sus territorios
- [Lista completa segun Seccion 6 de este documento]

Al acceder a la Plataforma, usted declara y garantiza que NO es residente,
ciudadano, ni se encuentra fisicamente ubicado en ninguna Region
Restringida. El uso de VPN, proxies, u otras herramientas para evadir
restricciones geograficas constituye una violacion material de estos
Terminos y resultara en la terminacion inmediata de su cuenta y la
confiscacion de fondos.
```

---

## 5. Politica de Privacidad

### 5.1 Framework Basado en GDPR/CCPA (Aplicacion Offshore)

Aunque la entidad opere offshore, adoptar estandares GDPR/CCPA es una decision estrategica:

- **Reputacion:** Usuarios globales esperan estandares de privacidad modernos.
- **Futuro acceso a EU:** Si se decide entrar al mercado europeo, ya se tiene compliance.
- **Proteccion legal:** Demuestra buena fe y diligencia debida ante reguladores.
- **Alcance extraterritorial:** GDPR aplica a cualquier entidad que procese datos de residentes EU, independientemente de donde este incorporada la entidad.

### 5.2 Estructura de la Politica de Privacidad

```
POLITICA DE PRIVACIDAD / PRIVACY POLICY

Fecha de vigencia: [FECHA]
Ultima actualizacion: [FECHA]
Controlador de datos: [Nombre de la OpCo], [direccion registrada]
Data Protection Officer (DPO): [Nombre/Email]

TABLA DE CONTENIDOS:
1.  Informacion que Recopilamos
2.  Base Legal para el Procesamiento
3.  Como Utilizamos su Informacion
4.  Comparticion de Datos con Terceros
5.  Transferencias Internacionales de Datos
6.  Retencion de Datos
7.  Seguridad de Datos
8.  Sus Derechos
9.  Cookies y Tecnologias de Rastreo
10. Menores de Edad
11. Cambios a esta Politica
12. Contacto
```

### 5.3 Contenido Clave por Seccion

#### 5.3.1 Informacion que Recopilamos

| Categoria | Datos | Proposito |
|---|---|---|
| **Registro** | Email, username, password (hash) | Creacion de cuenta |
| **KYC Tier 1** | Nombre completo, fecha de nacimiento, pais, tipo y numero de documento de identidad | Verificacion de identidad basica, compliance AML |
| **KYC Tier 2** | Documento de identidad (imagen), selfie/liveness check, comprobante de domicilio, source of funds declaration | Verificacion avanzada, compliance AML/CFT |
| **Transaccional** | Historial de trades, depositos, retiros, balances, wallet addresses | Operacion del servicio, compliance, resolucion de disputas |
| **Tecnico** | Direccion IP, tipo de navegador, dispositivo, sistema operativo, cookies, logs de acceso | Seguridad, prevencion de fraude, geo-blocking |
| **Comunicaciones** | Contenido de mensajes de soporte, feedback | Atencion al cliente |

#### 5.3.2 Base Legal para el Procesamiento (GDPR Art. 6)

| Base Legal | Aplicacion |
|---|---|
| **Ejecucion de contrato** (Art. 6(1)(b)) | Procesamiento necesario para proveer el servicio (registro, trading, retiros). |
| **Obligacion legal** (Art. 6(1)(c)) | KYC/AML/CFT compliance, reporting a autoridades cuando legalmente requerido. |
| **Interes legitimo** (Art. 6(1)(f)) | Prevencion de fraude, seguridad de la plataforma, analytics anonimizados. |
| **Consentimiento** (Art. 6(1)(a)) | Marketing por email, cookies no esenciales, comparticion con terceros no esenciales. |

#### 5.3.3 Derechos del Usuario

Se reconoceran los siguientes derechos (alineados con GDPR Cap. III y CCPA):

- **Derecho de acceso:** Obtener copia de todos los datos personales procesados.
- **Derecho de rectificacion:** Corregir datos inexactos o incompletos.
- **Derecho de supresion ("right to be forgotten"):** Solicitar eliminacion de datos, sujeto a obligaciones legales de retencion (AML: 5-7 anos).
- **Derecho de portabilidad:** Recibir datos en formato estructurado y legible por maquina.
- **Derecho de oposicion:** Oponerse al procesamiento basado en interes legitimo.
- **Derecho a restringir procesamiento:** Solicitar limitacion del procesamiento.
- **Derecho a no ser sujeto de decisiones automatizadas:** Incluyendo profiling con efectos legales.
- **CCPA: Derecho a no venta:** "Do Not Sell My Personal Information" (aunque la plataforma declare no vender datos, el mecanismo debe existir).

**Nota critica:** El derecho de supresion tiene excepciones significativas para datos requeridos por AML/CFT. Datos de KYC y transaccionales deben retenerse por el periodo legalmente mandatorio (tipicamente 5-7 anos post-cierre de cuenta) incluso si el usuario solicita eliminacion.

#### 5.3.4 Transferencias Internacionales de Datos

```
Dado que operamos desde [jurisdiccion offshore], los datos personales de
usuarios en el Espacio Economico Europeo (EEE), Reino Unido, o jurisdicciones
con restricciones de transferencia seran protegidos mediante:

(a) Clausulas Contractuales Estandar (SCCs) aprobadas por la Comision Europea;
(b) Evaluacion de impacto de transferencia (Transfer Impact Assessment);
(c) Medidas tecnicas suplementarias (encriptacion end-to-end, pseudonimizacion,
    access controls estrictos).
```

#### 5.3.5 Retencion de Datos

| Tipo de Dato | Periodo de Retencion |
|---|---|
| Datos de cuenta (basicos) | Duracion de la cuenta + 2 anos |
| Datos KYC | 5 anos post-cierre de cuenta (obligacion AML) |
| Datos transaccionales | 7 anos post-transaccion (obligacion AML/contable) |
| Logs tecnicos (IP, acceso) | 12 meses |
| Comunicaciones de soporte | 3 anos |
| Cookies y analytics | Segun consentimiento, maximo 13 meses |

---

## 6. Requisitos de Geo-blocking

### 6.1 Lista de Paises/Regiones a Bloquear

#### Tier 1: Bloqueo Total Obligatorio (Sin Excepciones)

| Pais/Region | Razon |
|---|---|
| **Estados Unidos** (incluyendo territorios: Puerto Rico, Guam, Islas Virgenes Americanas, Samoa Americana, Islas Marianas del Norte) | CFTC enforcement activo contra prediction markets no registrados. Precedente CFTC v. Polymarket. Riesgo de accion penal federal. SEC podria clasificar shares como securities. |
| **Iran** | Sanciones OFAC (EE.UU.), EU, ONU. Lista SDN. Prohibido interactuar con personas/entidades iranies. |
| **Corea del Norte (DPRK)** | Sanciones OFAC, EU, ONU. Aislamiento financiero total. |
| **Siria** | Sanciones OFAC, EU. Embargo comprehensivo. |
| **Cuba** | Sanciones OFAC. Embargo comprehensivo (aunque en evolucion). |
| **Crimea, Donetsk, Luhansk** (regiones de Ucrania bajo ocupacion rusa) | Sanciones OFAC, EU. Prohibicion de transacciones. |
| **Myanmar (en parte)** | Sanciones selectivas OFAC/EU contra entidades militares. Bloqueo preventivo recomendado. |
| **Sudan / Sur de Sudan** | Sanciones selectivas. Alto riesgo AML/CFT. |

#### Tier 2: Bloqueo Recomendado (Alto Riesgo Regulatorio)

| Pais/Region | Razon |
|---|---|
| **Reino Unido** | FCA prohibicion de cripto-derivados a retail. Alto riesgo de enforcement. Bloquear hasta obtener clarity o licencia. |
| **Francia** | Regulacion agresiva de AMF contra plataformas cripto no registradas. Parte de MiCA. |
| **Alemania** | BaFin activa en enforcement. Clasificacion strict de cripto-derivados. |
| **Paises Bajos** | AFM/DNB enforcement agresivo. Conexion con Curacao puede complicar si se usa esa jurisdiccion. |
| **China continental** | Prohibicion total de trading cripto desde 2021. Enforcement activo. |
| **Rusia** | Sanciones comprehensivas post-2022. Restricciones SWIFT. Alto riesgo de sanctions evasion. |
| **India** | Regulacion cripto restrictiva. 30% tax + 1% TDS en cripto transactions. Prediction markets (e.g., gambling) ilegales en la mayoria de estados. |

#### Tier 3: Monitoreo Activo (Riesgo Moderado, Decision Case-by-Case)

| Pais/Region | Razon |
|---|---|
| **Brasil** | Regulacion en desarrollo. CVM activa. Evaluar acceso con restricciones. |
| **Japon** | FSA regulacion estricta de cripto exchanges. Prediction markets no claramente regulados. |
| **Corea del Sur** | Regulacion cripto strict. Prohibicion de anonimato en trading. |
| **Turquia** | Prohibicion de uso de cripto para pagos. Regulacion en desarrollo. |
| **Australia** | ASIC enforcement creciente en cripto. Prediction markets (Sportsbet, etc.) regulados como gambling. |
| **Canada** | CSA (Canadian Securities Administrators) ha actuado contra plataformas cripto no registradas. |

### 6.2 Implementacion Tecnica del Geo-blocking

El geo-blocking debe implementarse en multiples capas:

1. **Capa 1 - IP Geolocation:** Bases de datos como MaxMind GeoIP2 para detectar pais del usuario por IP. Bloquear acceso desde IPs de paises restringidos.

2. **Capa 2 - VPN/Proxy Detection:** Servicios como IPQualityScore, IP2Location, o Telesign para detectar VPNs, proxies, y nodos Tor. Flag y bloqueo de usuarios que intentan evadir restricciones.

3. **Capa 3 - KYC Verification:** Verificacion de documento de identidad confirma nacionalidad y residencia. Rechazar documentos de paises restringidos.

4. **Capa 4 - Phone Number Verification:** Bloquear numeros de telefono de paises restringidos.

5. **Capa 5 - Behavioral Monitoring:** Patterns de uso anomalos (horarios, idioma del dispositivo, timezone) pueden indicar evasion de geo-blocking.

6. **Capa 6 - Wallet Analysis:** Herramientas como Chainalysis, Elliptic, o TRM Labs para detectar wallets asociadas con jurisdicciones sanctioned o actividad ilicita.

---

## 7. Estructura de KYC por Tiers

### 7.1 Tier 0: Acceso Basico (Sin KYC)

| Aspecto | Detalle |
|---|---|
| **Requisitos** | Ninguno. Acceso anonimo. |
| **Permisos** | Navegar la plataforma. Ver mercados, precios, volumenes, historicos. Leer documentacion, FAQ, ayuda. |
| **Restricciones** | NO puede crear cuenta. NO puede depositar fondos. NO puede comprar/vender shares. NO puede participar en ningun mercado. NO puede acceder a APIs de trading. |
| **Proposito** | Permitir que potenciales usuarios exploren la plataforma antes de registrarse. Reducir friccion de onboarding. Funcionar como "demo" o escaparate. |

### 7.2 Tier 1: Verificacion Basica

| Aspecto | Detalle |
|---|---|
| **Requisitos de verificacion** | Email verificado. Nombre completo (auto-declarado). Fecha de nacimiento (auto-declarada, debe ser mayor de 18 anos). Pais de residencia (auto-declarado). Numero de documento de identidad (tipo + numero, sin imagen). Numero de telefono verificado (SMS OTP). Aceptacion de ToS y Privacy Policy. |
| **Limites operativos** | Depositos: hasta $10,000 USD equivalente por mes calendario. Retiros: hasta $10,000 USD equivalente por mes calendario. Trading: sin restricciones dentro del limite de balance. Numero de mercados: sin restriccion. |
| **Tiempo de verificacion** | Instantaneo a 24 horas (verificacion de email y telefono es automatica; revision manual solo si se detectan anomalias). |
| **Documentacion retenida** | Email, nombre, fecha de nacimiento, pais, tipo y numero de documento, numero de telefono, IP de registro, timestamp. |

### 7.3 Tier 2: Verificacion Completa (Full KYC)

| Aspecto | Detalle |
|---|---|
| **Requisitos de verificacion** | Todo lo de Tier 1, MAS: Documento de identidad con foto (pasaporte, DNI, o licencia de conducir) - imagen frontal y trasera. Selfie con verificacion de liveness (anti-spoofing). Comprobante de domicilio (utility bill, extracto bancario, o documento oficial con fecha de menos de 3 meses). Declaracion de source of funds (para depositos mayores a $50,000). Screening contra listas PEP (Politically Exposed Persons) y sanciones. |
| **Limites operativos** | Depositos: sin limite (sujeto a source of funds para montos altos). Retiros: sin limite (sujeto a verificacion adicional para montos > $100,000). Trading: sin restricciones. Acceso a API: completo. Acceso a mercados premium o de alto volumen: habilitado. |
| **Tiempo de verificacion** | 1-3 dias habiles. Verificacion automatizada via proveedor KYC (Onfido, Jumio, Sumsub, Veriff) + revision manual para casos flaggeados. |
| **Documentacion retenida** | Todo lo de Tier 1 + imagenes de documento, selfie/liveness data, comprobante de domicilio, resultados de screening PEP/sanciones, source of funds declaration, IP y device fingerprint de verificacion. |
| **Re-verificacion** | Cada 12-24 meses, o cuando se detecte un cambio significativo (nuevo pais, nuevos patterns de transaccion, informacion adversa en screening). |

### 7.4 Proveedor de KYC Recomendado

| Proveedor | Fortaleza | Costo Estimado |
|---|---|---|
| **Sumsub** | Amplia cobertura de documentos, liveness check robusto, buen soporte LATAM. Dashboard de compliance integrado. | $1.50 - $3.00 por verificacion |
| **Onfido** | Lider de mercado en identity verification. AI-powered document check. Integracion con reguladores EU. | $2.00 - $4.00 por verificacion |
| **Jumio** | Fuerte en liveness y anti-fraud. Usado por exchanges grandes (Coinbase). | $2.50 - $5.00 por verificacion |
| **Veriff** | Rapido, UX fluida, buen precio para startups. Cobertura de 190+ paises. | $1.00 - $2.50 por verificacion |

**Recomendacion:** Sumsub o Veriff para fase inicial por balance de cobertura, costo, y facilidad de integracion. Migrar a Onfido o Jumio si se requiere compliance mas riguroso para mercados regulados.

---

## 8. AML/CFT Framework

### 8.1 Objetivo y Alcance

El framework de Anti-Money Laundering (AML) y Counter-Financing of Terrorism (CFT) establece las politicas, procedimientos y controles para prevenir que la plataforma sea utilizada para:

- Lavado de activos (money laundering)
- Financiamiento del terrorismo
- Financiamiento de la proliferacion de armas de destruccion masiva
- Evasion de sanciones internacionales
- Fraude y actividades ilicitas

Este framework aplica a todos los empleados, contratistas, directores y agentes de la entidad operativa y sus subsidiarias.

### 8.2 Marco Regulatorio de Referencia

El framework se alinea con:

- **GAFI/FATF:** 40 Recomendaciones del Grupo de Accion Financiera Internacional, incluyendo la "Travel Rule" (Recomendacion 16) para transacciones cripto.
- **EU AMLD6:** Sexta Directiva Anti-Lavado de la Union Europea.
- **BSA/FinCEN (referencia):** Bank Secrecy Act de EE.UU. como referencia de best practices (aunque no sea directamente aplicable).
- **Regulacion local:** Normativa AML de la jurisdiccion de incorporacion (Panama: Ley 23 de 2015 / BVI: AML Regulations 2008, as amended).

### 8.3 Estructura de Compliance

#### 8.3.1 Roles Clave

| Rol | Responsabilidad |
|---|---|
| **MLRO (Money Laundering Reporting Officer)** | Responsable principal de compliance AML/CFT. Recibe y evalua reportes de actividad sospechosa internos. Presenta reportes (STR/SAR) a la autoridad competente. Debe estar radicado en la jurisdiccion de la OpCo o tener presencia regular. |
| **Deputy MLRO** | Suplente del MLRO. Cubre ausencias y actua como segundo nivel de revision. |
| **Compliance Committee** | Comite compuesto por MLRO, CEO/COO, CTO, y Legal Counsel. Se reune trimestralmente para revisar politicas, casos, y riesgos emergentes. |
| **Todo el personal** | Obligacion de reportar actividad sospechosa al MLRO. Capacitacion AML/CFT anual obligatoria. |

#### 8.3.2 Programa de Compliance

1. **Risk Assessment (Evaluacion de Riesgo):**
   - Evaluacion de riesgo a nivel empresa (Enterprise-Wide Risk Assessment) anual.
   - Categorias de riesgo: clientes, productos/servicios, canales de distribucion, jurisdicciones.
   - Metodologia: riesgo inherente vs. riesgo residual post-controles.

2. **CDD (Customer Due Diligence):**
   - CDD estandar para todos los usuarios Tier 1 y Tier 2.
   - EDD (Enhanced Due Diligence) para: PEPs, usuarios de jurisdicciones de alto riesgo (GAFI grey/black list), transacciones inusualmente grandes, source of funds no clara.

3. **Ongoing Monitoring (Monitoreo Continuo):**
   - Monitoreo automatizado de transacciones (reglas y ML-based).
   - Alertas por: transacciones que excedan umbrales, patterns inusuales (structuring, rapid movement, round-tripping), multiples cuentas vinculadas, transacciones con wallets flaggeadas (Chainalysis/Elliptic).

4. **Sanctions Screening:**
   - Screening automatico contra listas de sanciones en el onboarding y de forma continua.
   - Listas: OFAC SDN, EU Consolidated List, UN Security Council, HMT (UK), y listas locales aplicables.
   - Re-screening diario de toda la base de usuarios contra listas actualizadas.

5. **Transaction Monitoring Rules:**

| Regla | Umbral/Criterio | Accion |
|---|---|---|
| Deposito grande unico | > $10,000 USD eq. | Flag para revision. EDD si Tier 1 (escalado a Tier 2 requerido). |
| Depositos acumulados | > $25,000 USD eq. en 30 dias | Flag para revision. |
| Retiro rapido post-deposito | Retiro > 80% del deposito en < 24h sin actividad de trading | Flag alto. Posible layering. |
| Structuring | Multiples depositos justo bajo $10,000 en periodo corto | Flag alto. Posible smurfing. |
| Wallet de alto riesgo | Interaccion con wallet flaggeada por Chainalysis/Elliptic (mixers, darknet, ransomware, sanctioned) | Bloqueo inmediato. Investigacion. Posible STR. |
| Multiples cuentas | Mismo dispositivo, IP, o documento vinculado a mas de 1 cuenta | Flag. Investigacion. Posible cierre de cuentas duplicadas. |
| PEP detected | Resultado positivo en screening PEP | EDD obligatorio. Aprobacion del MLRO para mantener cuenta. Monitoreo reforzado. |
| Jurisdiccion de alto riesgo | Usuario de pais en grey list GAFI | EDD obligatorio. Restricciones de limites. |

### 8.4 Reporte de Actividad Sospechosa (STR/SAR)

**Proceso:**

1. **Deteccion:** Automatica (sistema de monitoreo) o manual (empleado detecta anomalia).
2. **Escalacion interna:** Se reporta al MLRO mediante sistema interno (formulario estructurado con: descripcion de la actividad, usuarios involucrados, montos, fechas, razon de sospecha).
3. **Investigacion:** El MLRO investiga en un plazo maximo de 48 horas habiles. Revisa historial del usuario, patrones, informacion de KYC, blockchain analytics.
4. **Decision:**
   - **Descartar:** Si la investigacion concluye que la actividad es legitima, se documenta la razon y se cierra el caso.
   - **Reportar:** Si persiste la sospecha, el MLRO prepara y presenta un STR/SAR a la Unidad de Inteligencia Financiera (UIF/FIU) de la jurisdiccion correspondiente.
   - **Escalar:** En casos criticos (terrorismo, sanciones), se toman medidas inmediatas (congelamiento de cuenta, reporte a autoridades).
5. **Tipping-off:** Esta PROHIBIDO informar al usuario que es sujeto de un STR/SAR o investigacion interna. Violacion de esta regla es causal de terminacion y puede tener consecuencias penales.

### 8.5 Blockchain Analytics y Travel Rule

#### 8.5.1 Blockchain Analytics

- Integrar con un proveedor de blockchain analytics: **Chainalysis KYT** (Know Your Transaction), **Elliptic Lens**, o **TRM Labs**.
- Todas las direcciones de deposito y retiro deben ser screened en tiempo real.
- Categorias de riesgo de wallets: sanctioned, darknet market, mixer/tumbler, ransomware, scam, gambling (alto volumen), exchange (bajo riesgo), DeFi protocol, unknown.

#### 8.5.2 Travel Rule (FATF Recomendacion 16)

Para transferencias de cripto-activos superiores a $1,000 USD equivalente (o el umbral aplicable en la jurisdiccion):

- Se debe obtener y transmitir informacion del originador y beneficiario.
- Informacion requerida: nombre, numero de cuenta/wallet, direccion o identificador nacional, y para el beneficiario: nombre e identificador de cuenta.
- Implementar mediante soluciones como: **Notabene**, **Sygna**, **TravelRule Protocol (TRP)**, o **OpenVASP**.
- Nota: La aplicabilidad de la Travel Rule depende de la jurisdiccion. En Panama/BVI la enforcement es limitada, pero implementarla proactivamente demuestra buena fe regulatoria.

### 8.6 Capacitacion

| Audiencia | Frecuencia | Contenido |
|---|---|---|
| Todo el personal | Anual (obligatoria) + al ingreso | Fundamentos AML/CFT, como identificar actividad sospechosa, proceso de escalacion, tipping-off, sanciones por incumplimiento. |
| Equipo de compliance | Trimestral | Actualizaciones regulatorias, nuevas tipologias de lavado, casos internos anonimizados, herramientas de monitoreo. |
| Alta direccion / Board | Semestral | Risk assessment actualizado, metricas de compliance, tendencias regulatorias, presupuesto de compliance. |
| Equipo tech | Al ingreso + segun necesidad | Implementacion tecnica de controles, integracion con proveedores KYC/AML, privacy by design. |

### 8.7 Record Keeping

Todos los registros de compliance deben mantenerse por un minimo de:

- **Registros KYC/CDD:** 5 anos post-cierre de la relacion comercial.
- **Registros de transacciones:** 7 anos post-transaccion.
- **STR/SAR internos:** 5 anos post-presentacion.
- **Registros de capacitacion:** 5 anos.
- **Comunicaciones de compliance:** 5 anos.
- **Risk assessments:** Indefinido (historial completo para auditorias).

### 8.8 Auditoria

- **Auditoria interna:** Anual, conducida por compliance o auditor interno.
- **Auditoria externa:** Cada 2 anos (o anualmente si regulatoriamente requerido), por firma independiente especializada en AML/CFT.
- **Hallazgos y remediacion:** Documentados con plan de accion y plazos. El Compliance Committee hace seguimiento.

---

## Anexo A: Cronograma de Implementacion Sugerido

| Fase | Actividad | Plazo Estimado |
|---|---|---|
| Semana 1-4 | Seleccion de jurisdiccion final y counsel local | 4 semanas |
| Semana 2-6 | Incorporacion de Fundacion y OpCo | 4-6 semanas |
| Semana 4-8 | Redaccion de ToS y Privacy Policy (version legal review) | 4 semanas |
| Semana 4-10 | Seleccion e integracion de proveedor KYC | 6 semanas |
| Semana 6-12 | Implementacion de geo-blocking (tech) | 6 semanas |
| Semana 6-12 | Contratacion de MLRO y setup de compliance | 6 semanas |
| Semana 8-14 | Integracion de blockchain analytics | 6 semanas |
| Semana 10-16 | Testing completo de KYC/AML flow | 6 semanas |
| Semana 14-18 | Auditoria de seguridad y compliance pre-launch | 4 semanas |
| Semana 16-20 | Soft launch (beta cerrado) con compliance activo | 4 semanas |

## Anexo B: Presupuesto Estimado de Compliance (Ano 1)

| Item | Costo Estimado (USD) |
|---|---|
| Counsel legal (estructuracion + ongoing) | $60,000 - $120,000 |
| MLRO (salario o consultoria) | $80,000 - $150,000 |
| Proveedor KYC (Sumsub/Veriff) | $15,000 - $50,000 (volumen-dependiente) |
| Blockchain analytics (Chainalysis/Elliptic) | $50,000 - $100,000 |
| Screening de sanciones (ComplyAdvantage, Refinitiv) | $10,000 - $30,000 |
| Auditoria externa AML | $15,000 - $30,000 |
| Capacitacion | $5,000 - $10,000 |
| Herramientas tech (geo-blocking, VPN detection) | $10,000 - $25,000 |
| **TOTAL ESTIMADO ANO 1** | **$245,000 - $515,000** |

---

*Documento preparado como analisis preliminar para Phase 0.1. Sujeto a revision y actualizacion continua conforme evolucionen las regulaciones aplicables y se definan decisiones de jurisdiccion y estructura final.*

---

## 9. Regulacion de Servicios de Pago y Fiat Onramp

### 9.1 Arquitectura Regulatoria del Fiat Onramp

**Principio fundamental:** Praxis NO debe tocar fondos fiat directamente. Todo flujo fiat debe ser intermediado por PSPs licenciados (MoonPay, Transak, u otros) que conviertan fiat a USDC antes de que los fondos ingresen al ecosistema de la plataforma.

```
Flujo obligatorio:
  Usuario (tarjeta/banco) → PSP licenciado → Conversion a USDC → Wallet Praxis

Flujo PROHIBIDO:
  Usuario (tarjeta/banco) → Cuenta bancaria de Praxis → Plataforma
```

**Justificacion:** Si Praxis recibe, custodia, o transmite fondos fiat (incluso transitoriamente), se activan obligaciones de Money Transmitter (EE.UU./FinCEN), Electronic Money Institution (EU/PSD2), o Institucion de Pago (Brasil/BCB) que requieren licencias especificas, capital regulatorio, y supervision continua. Al interactuar solo con USDC post-conversion, Praxis mantiene su clasificacion como plataforma crypto-native.

### 9.2 Requisitos para PSPs

Los PSPs integrados deben cumplir con:

| Requisito | Detalle |
|---|---|
| **Licencias** | El PSP debe tener licencias de money transmission / EMI en las jurisdicciones donde opera |
| **KYC del PSP** | El PSP realiza su propio KYC al usuario. Praxis mantiene sus propios tiers independientemente |
| **Matching de identidad** | La identidad verificada por el PSP debe coincidir con la registrada en Praxis. Depositos de terceros no permitidos |
| **MCC Code** | Verificar que el PSP no clasifique las transacciones bajo MCC 7995 (gambling). Preferir "financial services" o "digital assets" |
| **Chargeback handling** | Acuerdo contractual con el PSP que defina responsabilidades en caso de chargebacks |
| **Jurisdicciones cubiertas** | El PSP debe geo-bloquear las mismas jurisdicciones que Praxis |
| **Reportes** | El PSP debe proveer reportes de transacciones para compliance AML de Praxis |

### 9.3 Monedas Fiat Soportadas y Riesgo Regulatorio

| Moneda | Riesgo | Decision MVP | Justificacion |
|---|---|---|---|
| **USD** | CRITICO | NO INCLUIR | Aceptar USD contradice la estrategia de zero-nexo con EE.UU. (Seccion 3.1.1). Depositos en USD crean argumento de nexo para CFTC/FinCEN |
| **EUR** | ALTO | INCLUIR CON RESTRICCIONES | Solo via PSP que convierta a USDC. No mantener balances en EUR. No marketing dirigido a EU salvo que se obtenga licencia MiCA/CASP |
| **BRL** | ALTO | NO INCLUIR EN MVP | Contradice la recomendacion de geo-blocking de Brasil (Seccion 3.1.5). Aceptar BRL constituye "soliciting" al mercado brasileno y activa regulacion CVM/BCB |

### 9.4 Proteccion contra Chargebacks

#### 9.4.1 Controles Tecnicos

- **3D Secure (3DS2) obligatorio** en todas las transacciones con tarjeta
- **Periodo de espera post-deposito**: fondos depositados via tarjeta no son retirables por 14 dias calendario
- **Limites de deposito escalonados**: primer deposito maximo $200, incrementa con historial
- **Device fingerprinting y velocity checks** para detectar fraude

#### 9.4.2 Controles Legales (agregar a ToS seccion 7)

Todo deposito fiat es convertido a USDC al tipo de cambio vigente. Una vez convertido, es final e irreversible a nivel de la plataforma. Los fondos depositados via tarjeta estan sujetos a un periodo de retencion de 14 dias durante el cual no pueden ser retirados, pero si pueden usarse para trading. La iniciacion de un chargeback fraudulento resulta en suspension de cuenta, confiscacion de balances, y reporte a servicios de prevencion de fraude. No se aceptan depositos desde instrumentos de pago que no pertenezcan al titular de la cuenta.

#### 9.4.3 Controles Operativos

- Mantener chargeback ratio por debajo de 0.5%
- Proceso de representment automatizado con evidencia de 3DS, IP match, actividad post-deposito
- Provisionar 5-10% del volumen de depositos fiat como reserva para chargebacks

### 9.5 Money Transmission: Analisis por Jurisdiccion

| Jurisdiccion | Riesgo MT | Mitigacion | Licencia requerida si se toca fiat |
|---|---|---|---|
| **EE.UU.** | CRITICO | Zero-nexo. No aceptar USD. PSP maneja todo fiat | MSB/MTL (estado por estado) - NO VIABLE |
| **EU** | ALTO | PSP convierte a USDC. Praxis no custodia EUR | EMI o PI bajo PSD2 si se custodia EUR |
| **Brasil** | ALTO | No aceptar BRL en MVP | Instituicao de Pagamento (BCB) |
| **UK** | ALTO | Geo-blocking UK. No aceptar GBP | PI/EMI bajo PSR 2017 |
| **Panama** | BAJO | Regulacion MT limitada | No aplica actualmente |
| **BVI** | MODERADO | Payment Systems Act podria aplicar | Evaluar con counsel local |

### 9.6 Disclaimer de Procesamiento de Pagos (agregar a ToS seccion 4.2)

Los servicios de deposito y retiro de fondos fiat son proporcionados por proveedores de servicios de pago terceros independientes ("PSPs"). La Plataforma NO es un servicio de pago, transmisor de dinero, institucion de dinero electronico, ni institucion de pago en ninguna jurisdiccion. La Plataforma no recibe, custodia, ni transmite fondos en moneda fiduciaria. Los PSPs son entidades independientes con sus propios terminos de servicio, politicas de privacidad, y requisitos regulatorios.

---

## 10. Estructura Legal para Multibranding

### 10.1 Modelo Operativo

El esquema de multiples marcas sobre un mismo engine tecnologico requiere separacion juridica para aislar riesgo regulatorio, permitir reglas de compliance diferenciadas, y proteger marcas de problemas de otras marcas del portfolio.

### 10.2 Estructura Recomendada

Cada marca debe operarse a traves de una entidad juridica separada (subsidiary de la OpCo o de la Fundacion):

| Componente | Requisito |
|---|---|
| **Entidad propia** | Cada marca = una entity |
| **ToS y Privacy Policy propios** | Adaptados a jurisdiccion target |
| **Licencias independientes** | La licencia la obtiene la entity de esa marca |
| **Compliance officer por marca** | O procedures diferenciados documentados |
| **Separacion de datos** | Datos de usuarios logicamente separados |
| **Contrato de licencia de tecnologia** | La OpCo licencia el engine a cada entity via Technology License Agreement |

### 10.3 Riesgo Principal: Piercing the Corporate Veil

Si un regulador determina que las multiples marcas son una unica operacion (mismos directores, cuentas, oficina), puede levantar el velo societario. Para evitar esto: directores distintos (al menos parcialmente), cuentas bancarias/wallets separadas, decision-making documentado, board minutes independientes, contratos inter-company a precios de mercado.
