[Contenidos](../Contenidos.md) \| [Anterior (4 Numpy)](04_Numpy.md) \| [Próximo (6 Algoritmos de búsqueda)](06_5_BSec_BBin_Viejo.md)

# 3.5 Búsqueda binaria

La clase pasada vimos la búsqueda secuencial de un elemento en una lista.


## Búsqueda sobre listas ordenadas

Si podemos suponer que la lista está previamente ordenada,
¿podemos encontrar una manera más eficiente de buscar elementos sobre ella?

En principio hay una modificación muy simple que podemos hacer sobre el
algoritmo de búsqueda lineal: si estamos buscando el elemento *e* en una
lista que está ordenada de menor a mayor, en cuanto encontremos algún elemento
mayor a *e* podemos estar seguros de que *e* no está en la lista, por lo que no
es necesario continuar recorriendo el resto.

### Ejercicio 3.10: Búsqueda lineal sobre listas ordenadas.Modificar la búsqueda lineal para el caso de listas ordenadas.
En el peor caso, ¿cuál es nuestra nueva hipótesis sobre comportamiento del
algoritmo? ¿Es realmente más eficiente?}

### Búsqueda binaria

¿Podemos hacer algo mejor? Trataremos de aprovechar el hecho de que la lista
está ordenada y vamos a hacer algo distinto: nuestro espacio de búsqueda se
irá achicando a segmentos cada vez menores de la lista original.
La idea es descartar segmentos de la lista donde el valor seguro que no puede
estar:

1. Consideramos como segmento inicial de búsqueda a la lista completa.

2. Analizamos el punto medio del segmento (el valor central); si es el valor
buscado, devolvemos el índice del punto medio.

3. Si el valor central es mayor al buscado, podemos descartar el segmento
que está desde el punto medio hacia la a derecha.

4. Si el valor central es menor al buscado, podemos descartar el segmento
que está desde el punto medio hacia la izquierda.

5. Una vez descartado el segmento que no nos interesa, volvemos a analizar
el segmento restante, de la misma forma.

6. Si en algún momento el segmento a analizar tiene longitud 0
significa que el valor buscado no se encuentra en la lista.


Para señalar la porción del segmento que se está analizando a cada paso,
utilizaremos dos variables (`izq` y `der`) que
contienen la posición de inicio y la posición de fin del segmento que se
está considerando. De la misma manera usaremos la varible `medio`
para contener la posición del punto medio del segmento.

A continuación ilustramos qué pasa cuando se busca
el valor 18 en la lista `[1, 3, 5, 7, 9, 11, 13, 15, 17, 19, 21, 23]`.

![Búsqueda Binaria](./bbin.png)
Ejemplo de una búsqueda usando el algoritmo de búsqueda binaria.
Como no se encontró al valor buscado, devuelve -1.

En el archivo `bbin.py` mostramos una posible implementación de
este algoritmo, incluyendo una instrucción de depuración (debug) con `print` para verificar su funcionamiento.

A continuación varias ejecuciones de prueba:

```python
>>> busqueda_binaria([1, 3, 5], 0)
[DEBUG] izq: 0 der: 2 medio: 1
[DEBUG] izq: 0 der: 0 medio: 0
-1
>>> busqueda_binaria([1, 3, 5], 1)
[DEBUG] izq: 0 der: 2 medio: 1
[DEBUG] izq: 0 der: 0 medio: 0
0
>>> busqueda_binaria([1, 3, 5], 2)
[DEBUG] izq: 0 der: 2 medio: 1
[DEBUG] izq: 0 der: 0 medio: 0
-1
>>> busqueda_binaria([1, 3, 5], 3)
[DEBUG] izq: 0 der: 2 medio: 1
1
>>> busqueda_binaria([1, 3, 5], 5)
[DEBUG] izq: 0 der: 2 medio: 1
[DEBUG] izq: 2 der: 2 medio: 2
2
>>> busqueda_binaria([1, 3, 5], 6)
[DEBUG] izq: 0 der: 2 medio: 1
[DEBUG] izq: 2 der: 2 medio: 2
-1
>>> busqueda_binaria([], 0)
-1
>>> busqueda_binaria([1], 1)
[DEBUG] izq: 0 der: 0 medio: 0
0
>>> busqueda_binaria([1], 3)
[DEBUG] izq: 0 der: 0 medio: 0
-1
```

**Pregunta**: En la línea `medio = (izq + der) // 2` efectuamos la división usando el operador `//` en lugar de `/`. ¿Qué pasa su usáramos `/`?

### ¿Cuántas comparaciones hace este programa?

Para responder esto pensemos en el peor caso, es decir, que se descartaron
varias veces partes del segmento para finalmente llegar a un segmento vacío y
el valor buscado no se encontraba en la lista.

En cada paso el segmento se divide por la mitad y se desecha una de esas
mitades, y en cada paso se hace una comparación con el valor buscado. Por lo
tanto, la cantidad de comparaciones que hacen con el valor buscado es
aproximadamente igual a la cantidad de pasos necesarios para llegar a un
segmento de tamaño 1.
Veamos el caso más sencillo para razonar, y supongamos que la longitud de la
lista es una potencia de 2, es decir `len(lista)`*= 2^k*:

1. Luego del primer paso, el segmento a tratar es de tamaño *2^k*.
2. Luego del segundo paso, el segmento a tratar es de tamaño *2^(k-1)*.
3. Luego del tercer paso, el segmento a tratar es de tamaño *2^(k-2)*.
...
4. Luego del paso *k*, el segmento a tratar es de tamaño *2^(k-k)=1*.


Por lo tanto este programa hace aproximadamente *k* comparaciones con el valor
buscado cuando `len(lista)`*= 2^k*.
Pero si despejamos *k* de la ecuación anterior, podemos ver que este programa
realiza aproximadamente `log2(len(lista)` comparaciones.

Cuando `len(lista)` no es una potencia de 2 el razonamiento es menos
prolijo, pero también vale que este programa realiza aproximadamente
`log2(len(lista))` comparaciones. Concluimos entonces que:

### Comparción entre ambos métodos

Veamos un ejemplo para entender cuánto más eficiente es la búsqueda binaria.
Supongamos que tenemos una lista con un millón de elementos.

1. El algoritmo de búsqueda lineal hará una cantidad de operaciones proporcional a un millón; es decir que en el peor caso hará 1,000,000 comparaciones, y en un caso promedio, 500,000 comparaciones.
2. El algoritmo de búsqueda binaria hará como máximo *log2(1,000,000)*
comparaciones, o sea ¡no más que 20 comparaciones!.

*Conclusión*: Si una lista está previamente ordenada, podemos utilizar el
algoritmo de búsqueda binaria, cuyo comportamiento es proporcional al
*logaritmo* de la cantidad de elementos de la lista, y por lo tanto
muchísimo más eficiente que la búsqueda lineal, espcialmente si la lista es larga.


[Contenidos](../Contenidos.md) \| [Anterior (4 Numpy)](04_Numpy.md) \| [Próximo (6 Algoritmos de búsqueda)](06_5_BSec_BBin_Viejo.md)
