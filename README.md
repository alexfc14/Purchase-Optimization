Modelo de Optimización para la Planificación de Compras
Descripción del Proyecto
Este proyecto presenta un modelo de optimización, implementado en Python con la librería PuLP, diseñado para ayudar a las empresas a planificar sus compras. El objetivo principal es minimizar el costo total, que incluye los costos de adquisición de productos, el almacenamiento de inventario y los costos fijos asociados a la realización de pedidos.

El modelo determina la cantidad óptima de cada producto a comprar a cada proveedor en cada período de tiempo, considerando las restricciones de demanda, capacidad de los proveedores y espacio de almacenamiento.

Documento Técnico del Modelo
1. Objetivo del Modelo
Este modelo matemático, formulado como un problema de programación lineal de enteros mixtos (MILP), tiene como objetivo principal minimizar el costo total de las compras, el almacenamiento y los pedidos fijos para una empresa a lo largo de un período de tiempo.

El modelo determina las siguientes variables para cada producto, proveedor y período:

Cantidad a comprar de cada producto a cada proveedor.

Cantidad de stock a mantener al final de cada período.

Si se realiza o no un pedido a un proveedor en un período dado.

2. Parámetros del Problema
El modelo utiliza los siguientes datos de entrada para encontrar la solución óptima:

K: Número de productos

M: Número de proveedores

T: Número de períodos de tiempo

D_i,t: Demanda del producto i en el período t.

S_i,0: Stock inicial del producto i.

C_i,j,t: Costo de compra por unidad del producto i al proveedor j en el período t.

H_i: Costo de almacenamiento por unidad del producto i por período.

S_cap_i,j,t: Capacidad de suministro del proveedor j para el producto i en el período t.

min_p_i,j,t: Cantidad mínima de pedido del producto i al proveedor j en el período t.

F_i,j,t: Costo fijo por realizar un pedido del producto i al proveedor j en el período t.

V_i: Volumen por unidad del producto i.

C_cap: Capacidad total de almacenamiento del almacén.

3. Variables de Decisión
Las variables del modelo son las que se ajustan para encontrar la solución óptima:

p_i,j,t: Cantidad de producto i a comprar al proveedor j en el período t (variable continua).

s_i,t: Cantidad de stock del producto i al final del período t (variable continua).

y_i,j,t: Variable binaria que indica si se realiza un pedido (1) o no (0) para el producto i al proveedor j en el período t.

4. Formulación Matemática
Función Objetivo
El objetivo es minimizar el costo total, que se compone de tres elementos:

Costo de Compra: ∑_i,j,tC_i,j,t⋅p_i,j,t

Costo de Almacenamiento: ∑_i,tH_i⋅s_i,t

Costo Fijo de Pedido: ∑_i,j,tF_i,j,t⋅y_i,j,t

Minimizar: (∑_i,j,tC_i,j,t⋅p_i,j,t)+(∑_i,tH_i⋅s_i,t)+(∑_i,j,tF_i,j,t⋅y_i,j,t)

Restricciones
Las siguientes restricciones aseguran que la solución sea factible:

Balance de Inventario: El stock final de un período es igual al stock inicial más las compras, menos la demanda.
S_i,t=S_i,t−1+∑_jp_i,j,t−D_i,t

∀i,t0
Para el primer período (t=0):
S_i,0+∑_jp_i,j,0−D_i,0=S_i,0

Capacidad del Proveedor: La cantidad comprada no puede exceder la capacidad de suministro del proveedor.
p_i,j,t≤S_cap_i,j,t

∀i,j,t

Pedido Mínimo: Si se realiza un pedido (y=1), la cantidad comprada debe ser al menos la cantidad mínima de pedido.
p_i,j,t≥min_p_i,j,t⋅y_i,j,t

∀i,j,t

Vinculación del Pedido: Esta restricción asegura que si no se realiza un pedido (y=0), la cantidad comprada sea cero.
p_i,j,t≤S_cap_i,j,t⋅y_i,j,t

∀i,j,t

Capacidad del Almacén: El volumen total del stock al final de cada período no puede exceder la capacidad máxima del almacén.
∑_is_i,t⋅V_i≤C_cap

∀t

6. Estructura del Código y Clases
El código está estructurado de manera modular para facilitar su comprensión, mantenimiento y reutilización. Se divide en tres clases principales:

ProblemParameters: Una clase simple que sirve como un contenedor de datos. Su función es agrupar todos los parámetros de entrada del problema de optimización (costos, demandas, capacidades, etc.) para que puedan ser pasados fácilmente al modelo.

PurchaseModel: La clase principal donde se formula el modelo de optimización. Aquí se definen las variables de decisión de PuLP, se construye la función objetivo y se añaden todas las restricciones matemáticas. El método solve() es el encargado de ejecutar el "solver" y encontrar la solución.

Solution: Una clase diseñada para procesar y presentar los resultados del modelo. Su función es tomar la solución cruda del "solver" y formatearla en un formato legible, como un DataFrame de Pandas, que muestra las cantidades óptimas a comprar, el stock final y el costo total.

7. Pruebas y Uso
Requisitos
Para ejecutar el modelo, necesitas instalar las siguientes librerías:

PuLP

NumPy

Pandas

Además, se requiere un "solver" para resolver el problema de optimización. El código está configurado para usar GLPK, que se instala automáticamente en entornos como Google Colab si no está presente.

# Instalación de PuLP, pandas y glpk (solver)
pip install pulp pandas
sudo apt-get install -y glpk-utils

Ejecución del Modelo
Para ejecutar el modelo de optimización, sigue estos pasos:

Importa las clases necesarias:

from pulp import *
from your_module import ProblemParameters, PurchaseModel, Solution

(Nota: Reemplaza your_module con el nombre de tu archivo si no está en el mismo script.)

Define los parámetros del problema: Puedes usar la función de generación de datos simulados o pasar tus propios datos.

# Ejemplo de datos simulados
params = ProblemParameters.create_example_parameters(
    num_products=3, num_providers=2, num_periods=4
)

Crea una instancia del modelo y resuelve:

model = PurchaseModel(params)
solution = model.solve()

Muestra la solución:

if solution:
    solution.display()
else:
    print("El modelo no encontró una solución óptima.")

Pruebas de Funcionamiento
El modelo incluye una serie de pruebas unitarias para validar su comportamiento en escenarios específicos, como:

Demanda y precio variables: El modelo debe comprar por adelantado si los precios futuros son significativamente más altos que los costos de almacenamiento.

Proveedor mayorista: El modelo debe considerar el pedido mínimo y optar por un proveedor más caro si el pedido mínimo del mayorista es demasiado grande.

Capacidad de almacenamiento cero: El modelo debe comprar exactamente la demanda para cada período si no puede almacenar inventario.

Escenario de liquidación: El modelo no debe realizar compras si el stock inicial es suficiente para cubrir toda la demanda.
