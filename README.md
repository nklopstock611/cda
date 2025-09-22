# cda
Repo para la materia Ciencia de Datos.


# Proyecto de Análisis de Cancelaciones en Reservas Hoteleras

## Integrantes del Equipo

* \[Nicolás Klopstock Triana]
* \[Erich Giusseppe Soto Parada]

*(Reemplazar con los nombres reales del equipo.)*

## Objetivo

Disminuir la mora y empezar a identificar estrategias efectivas para reducir la probabilidad de cancelación en reservas hoteleras, priorizando segmentos críticos y condiciones operativas asociadas al riesgo.

## Alcance

Este proyecto abarca:

* Análisis descriptivo de cancelaciones por hotel, canal y segmento.
* Identificación de variables clave asociadas al riesgo (e.g., `lead_time`, `adr`, tipo de depósito).
* Construcción de clústers para detectar perfiles extremos de cancelación y contrastarlos con grupos de bajo riesgo.
* Desarrollo de un árbol de decisión para generar reglas operativas accionables.
* Recomendaciones inmediatas para la operación, la calidad de datos y la gobernanza.
* Lineamientos iniciales para diseñar intervenciones preventivas en segmentos de alto riesgo.

## Conclusiones (Insights)

### Qué vimos

El descriptivo muestra concentración de riesgo en *City Hotel* (Fig.\~\ref{fig\:hotel}) y en canales/segmentos *Online TA* y *TA/TO* (Fig.\~\ref{fig\:segments}), con señales numéricas donde *lead\_time* y *adr* se asocian con mayor probabilidad de cancelación (Fig.\~\ref{fig\:corr}). El *clustering* identifica un grupo extremo (tasa cercana a 1.0 y \$n\$ relevante) que combina *lead\_time* muy alto, *TA/TO* y *No Refund*. El árbol confirma reglas simples y accionables: las particiones por *deposit\_type\_No Refund*, *hotel\_City Hotel*, *lead\_time* y *market\_segment\_Online TA* capturan buena parte del riesgo.

### Acciones inmediatas (dónde intervenir)

* **City Hotel** (Fig.\~\ref{fig\:hotel}): investigar causas específicas del mayor porcentaje de cancelación (mix de canal, pricing, experiencia); priorizar acciones allí por su combinación de tasa alta y volumen.
* **Canales/segmentos de alto aporte al problema** (Fig.\~\ref{fig\:segments}): enfocar revisión en *Online TA* y *TA/TO* (proporción y volumen altos). Objetivo: entender por qué cancelan más y qué ajustes de proceso/condiciones reducen la deserción.
* **Clientes *Transient*** (Fig.\~\ref{fig\:segments}): analizar por qué concentran mayor cancelación (anticipación, canal, precio) y documentar prácticas que funcionan en subgrupos de menor riesgo.
* **Reservas con *lead\_time* largo y *adr* alto** (Fig.\~\ref{fig\:corr}): priorizar su seguimiento por mayor propensión e impacto económico; definir umbrales operativos de atención temprana basados en estas dos señales.
* **Room types con mayor peso en el riesgo** (Fig.\~\ref{fig\:rooms}): revisar expectativas y pricing del tipo A (tanto reservado como asignado); verificar si hay desalineación entre promesa y experiencia.

### Acciones de calidad de datos y gobernanza

* **Auditar *deposit\_type\_No Refund***: la tasa observada es contraintuitiva; validar significado, momento de registro y consistencia histórica antes de usarla para decisiones.
* **Revisar variables cercanas a operación tardía** (p.ej., *assigned\_room\_type*, *room\_mismatch*; Fig.\~\ref{fig\:rooms} y Fig.\~\ref{fig\:mismatch}): confirmar su semántica y evitar *leakage* en modelos que predicen al momento de la reserva.
* **Etiquetas y categorías** (p.ej., unificación de “No Refund/Non Refund”, “Online TA/TA”): normalizar nomenclaturas para análisis y despliegues consistentes.

### Hallazgos de clústers (grupos críticos y rasgos operativos)

El análisis del codo y de la silueta (Figs.\~\ref{fig\:cluster-criterios}) sugiere \$k=4\$; la proyección bidimensional (Fig.\~\ref{fig\:cluster-pca}) hace visible un *grupo crítico* claramente separado del resto. Este clúster concentra una **tasa de cancelación cercana a 1.0** con \$n\$ relevante e integra un patrón operativo muy definido: *lead\_time* **muy alto**, *adr* por debajo del promedio, predominio de *TA/TO* (offline), cliente *Transient* y presencia total de *No Refund*.
Recomendaciones:

1. Trazar el flujo end-to-end de ese segmento (origen de demanda, acuerdos comerciales, mensajes y tiempos de reconfirmación).
2. Revisar la *semántica* y consistencia de *No Refund*.
3. Definir reglas de seguimiento y comunicación para *lead\_time* muy largos en TA/TO.
4. Evaluar ajustes de *pricing* y beneficios condicionados al *no show/cancelación*.

Los otros tres clústers muestran **tasas moderadas/bajas** con perfiles más heterogéneos (p.ej., uno asociado a *Resort*, *Online TA* y *No Deposit*, con cancelación baja \$\approx 0.15\$): estos sirven como **contraste operativo** para identificar prácticas que sí reducen cancelación (anticipación más corta, mejor comunicación y condiciones más claras).

### Hallazgos del árbol (variables clave, umbrales y relevancia)

El modelo supervisado (Fig.\~\ref{tab\:test-metrics} y Fig.\~\ref{tab\:top10-vars}) confirma pocas palancas, pero muy influyentes, y las organiza en reglas operables.

* El primer corte es *deposit\_type\_No Refund*: múltiples ramas convergen a cancelación → auditar definición antes de usarla.
* En ausencia de *No Refund*, el árbol usa *hotel\_City Hotel*, *lead\_time* y *market\_segment\_Online TA* para separar riesgo. Umbrales prácticos (ej. *lead\_time* ≈ 10–12 días) diferencian claramente la propensión.
* La relevancia de *customer\_type\_Transient* y *adr* indica que anticipación + canal + precio forman un triángulo crítico: reservas de *lead\_time* largo, en *City Hotel* y *Online TA*, con *adr* relativamente alto → deben entrar a listas de seguimiento temprano.

En síntesis, el árbol entrega:

1. Variables clave: *deposit\_type\_No Refund*, *hotel\_City Hotel*, *lead\_time*, *market\_segment\_Online TA*, *adr*.
2. Umbrales de decisión para *lead\_time* y combinaciones canal×hotel.
3. Reglas que pueden traducirse en alertas y priorización operativa.

## Instrucciones de Ejecución

1. Clonar el repositorio:

   ```bash
   git clone https://github.com/usuario/proyecto-cancelaciones.git
   cd cda
   ```
2. Instalar dependencias:

   ```bash
   pip install -r requirements.txt
   ```
3. Preparar los datos:
   - Crear una carpeta llamada `data/` en la raíz del proyecto.
   - Colocar dentro de esta carpeta todos los archivos de datos necesarios para la ejecución del notebook.
   
5. Ejecutar análisis principal:
   Ejecute celda por celda

   ```bash
   hotel_bookings_modified.ipynb
   ```

## Dependencias

* Python 3.10+
* Pandas
* NumPy
* Matplotlib / Seaborn
* Scikit-learn
* Jupyter Notebook (opcional, para exploración interactiva)

