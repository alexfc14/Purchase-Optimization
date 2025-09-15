# **Modelo de Optimización para la Planificación de Compras**

## **Descripción del Proyecto**

Este proyecto presenta un modelo de optimización, implementado en Python con la librería PuLP, diseñado para ayudar a las empresas a planificar sus compras. El objetivo principal es **minimizar el costo total**, que incluye los costos de adquisición de productos, el almacenamiento de inventario y los costos fijos asociados a la realización de pedidos.

El modelo determina la cantidad óptima de cada producto a comprar a cada proveedor en cada período de tiempo, considerando las restricciones de demanda, capacidad de los proveedores y espacio de almacenamiento.

## **Documento Técnico del Modelo**

### **1\. Objetivo del Modelo**

Este modelo matemático, formulado como un problema de programación lineal de enteros mixtos (MILP), tiene como objetivo principal minimizar el costo total de las compras, el almacenamiento y los pedidos fijos para una empresa a lo largo de un período de tiempo.

El modelo determina las siguientes variables para cada producto, proveedor y período:

* Cantidad a comprar de cada producto a cada proveedor.  
* Cantidad de stock a mantener al final de cada período.  
* Si se realiza o no un pedido a un proveedor en un período dado.

### **2\. Parámetros del Problema**

El modelo utiliza los siguientes datos de entrada para encontrar la solución óptima:

* K: Número de productos  
* M: Número de proveedores  
* T: Número de períodos de tiempo  
* D\_i,t: Demanda del producto i en el período t.  
* S\_i,0: Stock inicial del producto i.  
* C\_i,j,t: Costo de compra por unidad del producto i al proveedor j en el período t.  
* H\_i: Costo de almacenamiento por unidad del producto i por período.  
* S\_cap\_i,j,t: Capacidad de suministro del proveedor j para el producto i en el período t.  
* min\_p\_i,j,t: Cantidad mínima de pedido del producto i al proveedor j en el período t.  
* F\_i,j,t: Costo fijo por realizar un pedido del producto i al proveedor j en el período t.  
* V\_i: Volumen por unidad del producto i.  
* C\_cap: Capacidad total de almacenamiento del almacén.

### **3\. Variables de Decisión**

Las variables del modelo son las que se ajustan para encontrar la solución óptima:

* p\_i,j,t: Cantidad de producto i a comprar al proveedor j en el período t (variable continua).  
* s\_i,t: Cantidad de stock del producto i al final del período t (variable continua).  
* y\_i,j,t: Variable binaria que indica si se realiza un pedido (1) o no (0) para el producto i al proveedor j en el período t.

### **4\. Formulación Matemática**

#### **Función Objetivo**

El objetivo es minimizar el costo total, que se compone de tres elementos:

1. **Costo de Compra:** ∑\_i,j,tC\_i,j,t⋅p\_i,j,t  
2. **Costo de Almacenamiento:** ∑\_i,tH\_i⋅s\_i,t  
3. **Costo Fijo de Pedido:** ∑\_i,j,tF\_i,j,t⋅y\_i,j,t

Minimizar: (∑\_i,j,tC\_i,j,t⋅p\_i,j,t)+(∑\_i,tH\_i⋅s\_i,t)+(∑\_i,j,tF\_i,j,t⋅y\_i,j,t)

#### **Restricciones**

Las siguientes restricciones aseguran que la solución sea factible:

1. Balance de Inventario: El stock final de un período es igual al stock inicial más las compras, menos la demanda.  
   S\_i,t=S\_i,t−1+∑\_jp\_i,j,t−D\_i,tquadforalli,t0  
   Para el primer período (t=0):  
   S\_i,0+∑\_jp\_i,j,0−D\_i,0=S\_i,0  
2. Capacidad del Proveedor: La cantidad comprada no puede exceder la capacidad de suministro del proveedor.  
   p\_i,j,t≤S\_cap\_i,j,tquadforalli,j,t  
3. Pedido Mínimo: Si se realiza un pedido (y=1), la cantidad comprada debe ser al menos la cantidad mínima de pedido.  
   p\_i,j,t≥min\_p\_i,j,t⋅y\_i,j,tquadforalli,j,t  
4. Vinculación del Pedido: Esta restricción asegura que si no se realiza un pedido (y=0), la cantidad comprada sea cero.  
   p\_i,j,t≤S\_cap\_i,j,t⋅y\_i,j,tquadforalli,j,t  
5. Capacidad del Almacén: El volumen total del stock al final de cada período no puede exceder la capacidad máxima del almacén.  
   ∑\_is\_i,t⋅V\_i≤C\_capquadforallt

## **6\. Estructura del Código y Clases**

El código está estructurado de manera modular para facilitar su comprensión, mantenimiento y reutilización. Se divide en tres clases principales:

* **ProblemParameters**: Una clase simple que sirve como un contenedor de datos. Su función es agrupar todos los parámetros de entrada del problema de optimización (costos, demandas, capacidades, etc.) para que puedan ser pasados fácilmente al modelo.  
* **PurchaseModel**: La clase principal donde se formula el modelo de optimización. Aquí se definen las variables de decisión de PuLP, se construye la función objetivo y se añaden todas las restricciones matemáticas. El método solve() es el encargado de ejecutar el "solver" y encontrar la solución.  
* **Solution**: Una clase diseñada para procesar y presentar los resultados del modelo. Su función es tomar la solución cruda del "solver" y formatearla en un formato legible, como un DataFrame de Pandas, que muestra las cantidades óptimas a comprar, el stock final y el costo total.

## **7\. Pruebas y Uso**

### **Requisitos**

Para ejecutar el modelo, necesitas instalar las siguientes librerías:

* PuLP  
* NumPy  
* Pandas

Además, se requiere un "solver" para resolver el problema de optimización. El código está configurado para usar **GLPK**, que se instala automáticamente en entornos como Google Colab si no está presente.

\# Instalación de PuLP, pandas y glpk (solver)  
pip install pulp pandas  
sudo apt-get install \-y glpk-utils

### **Ejecución del Modelo**

Para ejecutar el modelo de optimización, sigue estos pasos:

1. **Importa las clases necesarias:**  
   from pulp import \*  
   from your\_module import ProblemParameters, PurchaseModel, Solution

   (Nota: Reemplaza your\_module con el nombre de tu archivo si no está en el mismo script.)  
2. **Define los parámetros del problema:** Puedes usar la función de generación de datos simulados o pasar tus propios datos.  
   \# Ejemplo de datos simulados  
   params \= ProblemParameters.create\_example\_parameters(  
       num\_products=3, num\_providers=2, num\_periods=4  
   )

3. **Crea una instancia del modelo y resuelve:**  
   model \= PurchaseModel(params)  
   solution \= model.solve()

4. **Muestra la solución:**  
   if solution:  
       solution.display()  
   else:  
       print("El modelo no encontró una solución óptima.")

### **Pruebas de Funcionamiento**

El modelo incluye una serie de pruebas unitarias para validar su comportamiento en escenarios específicos, como:

* **Demanda y precio variables:** El modelo debe comprar por adelantado si los precios futuros son significativamente más altos que los costos de almacenamiento.  
* **Proveedor mayorista:** El modelo debe considerar el pedido mínimo y optar por un proveedor más caro si el pedido mínimo del mayorista es demasiado grande.  
* **Capacidad de almacenamiento cero:** El modelo debe comprar exactamente la demanda para cada período si no puede almacenar inventario.  
* **Escenario de liquidación:** El modelo no debe realizar compras si el stock inicial es suficiente para cubrir toda la demanda.

---------

# **Documentación Técnica del Algoritmo Voraz (Greedy Backward)**

## **1\. Introducción**

El algoritmo implementado es una heurística de tipo "greedy" o voraz, diseñada para encontrar una solución rápida y eficiente al problema de optimización de la cadena de suministro, que considera costos de compra, almacenamiento y costos fijos por pedido. A diferencia de un modelo de optimización exacto como el de Gurobi, que garantiza el óptimo global, este enfoque se centra en tomar decisiones localmente óptimas en cada paso, lo que resulta en un tiempo de ejecución significativamente menor. El objetivo es obtener una solución de alta calidad en un período de tiempo razonable.

## **2\. Lógica del Algoritmo (Backward Greedy)**

La estrategia principal del algoritmo es un enfoque de "atrás hacia adelante" o "pull". En lugar de planificar las compras desde el presente, el algoritmo mira hacia el futuro y determina qué debe comprarse para satisfacer la demanda de cada período, priorizando las decisiones que generan el menor costo unitario en el momento de la elección.

El flujo de trabajo se estructura de la siguiente manera:

1. **Iteración por Período Inversa:** El algoritmo comienza por el último período (T) y avanza hacia el primer período (t=0). Esto permite que el plan de compras se "arrastre" hacia atrás desde la demanda, asegurando que el stock esté disponible en el momento en que se necesita.  
2. **Priorización de la Demanda:** Dentro de cada período, los productos se ordenan por su demanda. La heurística prioriza la satisfacción de la demanda de los productos más necesarios primero.  
3. **Selección del Mejor Candidato (\_find\_best\_candidate):** Para cada unidad de demanda de un producto, el algoritmo busca la opción de compra que minimice un **costo unitario proyectado**. Este costo unitario se calcula con una función de costo que incluye los siguientes componentes:  
   * **Costo de Compra:** El costo base del producto por unidad al proveedor y en el período de compra.  
   * **Costo de Almacenamiento:** Se calcula como el costo de almacenamiento por unidad multiplicado por el número de períodos que la unidad estará en el inventario. CostoAlmacenamientoTotal \= CostoAlmacenamiento \* (t\_demanda \- t\_compra).  
   * **Costo Fijo por Pedido:** Este costo se aplica una sola vez por proveedor y por período de compra. El algoritmo lo incorpora en el cálculo del costo unitario solo si no se ha realizado ninguna otra compra a ese proveedor en ese período. La condición para esto es que la suma de todas las compras a dicho proveedor en el período sea cero.  
4. **Asignación de la Compra:** Una vez que se identifica el mejor candidato (proveedor, período y cantidad de compra), la compra se asigna, se actualiza la capacidad restante del proveedor y se reduce la demanda pendiente. Este proceso continúa hasta que toda la demanda del producto en el período actual esté satisfecha.  
5. **Cálculo Final del Costo y el Stock:** Una vez que todas las decisiones de compra han sido tomadas, el algoritmo realiza una segunda pasada, esta vez en orden cronológico (t=0 a T), para calcular con precisión los niveles de stock finales y el costo total real de la solución, sumando todos los costos de compra, almacenamiento y costos fijos.

## **3\. Consideraciones y Limitaciones**

El enfoque voraz, aunque rápido, es inherentemente miope. Su principal limitación es que las decisiones tomadas en un período para minimizar el costo local pueden no ser las más beneficiosas para la solución general a largo plazo. Por ejemplo, una compra "barata" en un período temprano podría ocupar la capacidad del almacén, impidiendo compras aún más rentables más adelante. Sin embargo, como se ha demostrado, este algoritmo ofrece un excelente equilibrio entre velocidad y calidad de la solución, acercándose significativamente al óptimo.

## **4\. Estructura de la Solución**

El resultado del algoritmo se encapsula en una clase Solution que almacena el plan de compras, los niveles de stock y el costo total. Esta clase también realiza una serie de validaciones automáticas para asegurar que la solución respeta todas las restricciones del modelo, como la capacidad del almacén y los límites de los pedidos.


