import numpy as np
import pandas as pd
import matplotlib.pyplot as plt


def generar_procesos(num_procesos, tiempos_llegada, tiempos_ejecucion):
    procesos = [{'Proceso': f'P{i+1}',
                 'TiempoLlegada': tiempos_llegada[i],
                 'TiempoEjecucion': tiempos_ejecucion[i]} for i in range(num_procesos)]
    return procesos

def spn(procesos):
    procesos_filtrados = [p for p in procesos if 'TiempoEjecucion' in p]
    procesos_ordenados = sorted(procesos_filtrados, key=lambda x: x['TiempoEjecucion'])

    return procesos_ordenados

def calcular_retorno_espera(procesos):
    tiempo_retorno = [p['TiempoFinal'] - p['TiempoLlegada'] for p in procesos]
    tiempo_espera = [p['TiempoInicio'] - p['TiempoLlegada'] for p in procesos]
    return tiempo_retorno, tiempo_espera

def graficar_tabla_procesos(procesos):
    df = pd.DataFrame(procesos)
    df = df.sort_values(by='Proceso')
    print(df)

def graficar_grafico_grant(procesos):
    procesos_ordenados = sorted(procesos, key=lambda x: x['Proceso'], reverse=True)
    plt.figure(figsize=(8, 4))
    plt.barh([p['Proceso'] for p in procesos_ordenados], [p['TiempoEjecucion'] for p in procesos_ordenados], left=[p['TiempoInicio'] for p in procesos_ordenados])
    plt.xlabel('Tiempo')
    plt.ylabel('Proceso')
    plt.title('Gráfico de Grant (SPN)')
    plt.show()

num_procesos = 4
procesos_definidos = [
    {'Proceso': 'A', 'TiempoLlegada': 0, 'TiempoEjecucion': 6},
    {'Proceso': 'B', 'TiempoLlegada': 1, 'TiempoEjecucion': 3},
    {'Proceso': 'C', 'TiempoLlegada': 3, 'TiempoEjecucion': 2},
    {'Proceso': 'D', 'TiempoLlegada': 5, 'TiempoEjecucion': 4},
]

procesos = generar_procesos(num_procesos, [p['TiempoLlegada'] for p in procesos_definidos], [p['TiempoEjecucion'] for p in procesos_definidos])
procesos_spn = spn(procesos)
procesos_spn.insert(0, procesos_spn.pop(procesos_spn.index(next(p for p in procesos_spn if p['Proceso'] == 'P1'))))
tiempo_actual = 0
for proceso in procesos_spn:
    proceso['TiempoInicio'] = tiempo_actual
    proceso['TiempoFinal'] = tiempo_actual + proceso['TiempoEjecucion']
    tiempo_actual = proceso['TiempoFinal']


tiempo_retorno, tiempo_espera = calcular_retorno_espera(procesos_spn)

for i, proceso in enumerate(procesos_spn):
    proceso['TiempoRetorno'] = tiempo_retorno[i]
    proceso['TiempoEspera'] = tiempo_espera[i]

graficar_tabla_procesos(procesos_spn)
graficar_grafico_grant(procesos_spn)

tiempo_retorno_total = sum(p['TiempoRetorno'] for p in procesos_spn)
tiempo_espera_total = sum(p['TiempoEspera'] for p in procesos_spn)
promedio_retorno = tiempo_retorno_total / len(procesos_spn)
promedio_espera = tiempo_espera_total / len(procesos_spn)

print(f'Tiempo medio de retorno: {promedio_retorno:.2f}')
print(f'Tiempo medio de espera: {promedio_espera:.2f}')
