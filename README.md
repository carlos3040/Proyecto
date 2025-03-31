# Proyecto
Holis
import numpy as np
from scipy.integrate import solve_ivp

# Extraer los datos de cada programa
df_p1 = df[df["Carrera"] == "Programa 1"].pivot(index="Año", columns="Semestre", values="# Estudiantes")
df_p2 = df[df["Carrera"] == "Programa 2"].pivot(index="Año", columns="Semestre", values="# Estudiantes")

# Ordenar por año
df_p1 = df_p1.sort_index()
df_p2 = df_p2.sort_index()

# Definir el modelo de ecuaciones diferenciales
def matricula_dinamica(t, N, alpha, beta):
    dNdt = np.zeros_like(N)
    dNdt[0] = -beta[0] * N[0]  # Primera ecuación (entrada y deserción)
    for i in range(1, len(N)):
        dNdt[i] = alpha[i-1] * N[i-1] - beta[i] * N[i]
    return dNdt

# Estimación de parámetros iniciales (aproximados desde datos)
alpha_p1 = [0.8, 0.7, 0.6, 0.5]  # Probabilidades de promoción
beta_p1 = [0.1, 0.1, 0.1, 0.2, 0.3]  # Tasas de deserción

# Condiciones iniciales para Programa 1 (último año observado)
N0_p1 = df_p1.iloc[-1].values

# Intervalo de tiempo para proyección (2024-2030)
t_span = (2024, 2030)
t_eval = np.arange(2024, 2031, 1)

# Resolver el sistema de ecuaciones diferenciales
sol_p1 = solve_ivp(matricula_dinamica, t_span, N0_p1, t_eval=t_eval, args=(alpha_p1, beta_p1))

# Resultados proyectados
sol_p1.y
