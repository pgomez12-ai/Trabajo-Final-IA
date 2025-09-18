# 📌 Proyecto: Automatización de Alertas de Tipo de Cambio USD→COP con N8N

Este repositorio corresponde a la **Evaluación 03 - Diseño y Solución de Problemas con Agentes de IA** de la asignatura **Introducción a la Inteligencia Artificial**, dentro de la **Maestría en Ciencia de Datos y Analítica (Universidad EAFIT)**.

La solución presentada se construyó utilizando **N8N** para la orquestación de flujos de trabajo y automatización de procesos.

---

## 👨‍🎓 Autores

- Santiago Carvajal Torres - scarvajat1@eafit.edu.co
- Pedro Gómez Bedoya - pgomezb@eafit.edu.co

---

## 📖 Planteamiento del Problema

En entornos financieros y de negocio es fundamental detectar de manera temprana variaciones significativas en el **tipo de cambio USD→COP**.  
Una alerta automática permite a las empresas anticiparse a riesgos cambiarios y tomar decisiones oportunas (por ejemplo, cubrirse ante una devaluación o aprovechar un fortalecimiento).

El problema identificado es:
- Actualmente la revisión de tasas de cambio se realiza de forma manual.  
- No existe un mecanismo de notificación automática que alerte sobre cambios relevantes en la tasa.  
- Esto implica riesgo operativo y pérdida de oportunidades en la gestión cambiaria.

---

## ⚙️ Justificación de la Tecnología

Para resolver este problema se evaluaron dos tecnologías propuestas en el taller: **N8N** y **Streamlit**.

- **Streamlit** es ideal para aplicaciones interactivas y análisis exploratorio, pero requiere mayor desarrollo en Python y despliegue de servicios adicionales para notificaciones.  
- **N8N** ofrece integración nativa con múltiples APIs, lógica condicional y conectores de correo electrónico, lo cual lo hace perfecto para:  
  - Automatizar la consulta diaria de tasas de cambio.  
  - Aplicar reglas de comparación contra un umbral configurable.  
  - Enviar alertas automáticas por correo cuando se supere dicho umbral.  

👉 Por lo tanto, **N8N es la herramienta más adecuada** para este caso: garantiza rapidez de implementación, escalabilidad y mantenimiento sencillo.

---

## 🛠️ Descripción del Workflow y Guía de Uso

El flujo completo se encuentra exportado en [`Workflow.json`](./Workflow.json).  
A continuación se presenta su estructura, primero con una vista general y luego con el detalle nodo a nodo.

### 📸 Vista General del Flujo
![Flujo completo](./fotos/flujo_completo_envio.png)

---

### 1️⃣ Trigger (Diario 6 am)
- **Función:** Dispara la ejecución automática del flujo todos los días a las 6:00 a.m.  
- **Importancia:** Permite que la consulta y análisis de la tasa de cambio sea **totalmente autónoma**.  
![Trigger](./fotos/nodo_trigger.png)

---

### 2️⃣ Fecha Hoy–Ayer (JavaScript)
- **Función:** Calcula dinámicamente las fechas de hoy y ayer en formato `YYYY-MM-DD`.
- **Importancia:** Garantiza que siempre se comparen las tasas actuales contra las del día anterior sin intervención manual.  
![Fecha Hoy–Ayer](./fotos/nodo_fecha.png)

---

### 3️⃣ Solicitud Data HTTP
- **Función:** Consulta la API pública [`exchangerate.host`](https://exchangerate.host/) para obtener el valor de USD→COP en las fechas definidas.  
- **Importancia:** Provee los datos crudos sobre los cuales se calcula la variación.  
![HTTP Request](./fotos/nodo_http.png)

---

### 4️⃣ Umbral Alerta (Set Node)
- **Función:** Nodo manual donde se define el valor de `THRESHOLD_PCT`, que representa el % de variación que dispara la alerta.  
- **Importancia:** Permite **personalizar la sensibilidad** del sistema según las necesidades del usuario.  
![Umbral Alerta](./fotos/nodo_umbral.png)

---

### 5️⃣ Comparación Valor Anterior (JavaScript)
- **Función:** Calcula el porcentaje de variación entre el valor actual y el del día anterior, y lo compara contra el umbral.  
- **Importancia:** Constituye el **núcleo lógico** del flujo, pues decide si se debe o no enviar la alerta.  
![Comparación](./fotos/nodo_comparacion.png)

---

### 6️⃣ Condicional Envío Alerta (IF Node)
- **Función:** Evalúa si la variación calculada supera el umbral definido.  
- **Importancia:** Controla la separación del flujo, evitando enviar correos innecesarios.  
![Condicional](./fotos/nodo_if.png)

---

### 7️⃣ Enviar Mensaje Alerta (Gmail)

- **Función:** Envía un correo con los resultados: precio actual, precio anterior, % de variación y umbral configurado.  
- **Importancia:** Es la **salida final del flujo**, entregando la notificación al usuario de forma clara y oportuna.  

📸 **Configuración del nodo en N8N**  
![Nodo Gmail](./fotos/nodo_gmail.png)

📨 **Ejemplo de correo recibido**  
![Correo recibido](./fotos/mensaje_gmail.png)
