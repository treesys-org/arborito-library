@title: Complejidad algorítmica: O, Ω, Θ
@icon: 📈
@description: Notación asintótica, casos peor/promedio y análisis de recurrencias.
@order: 1

# Complejidad: predecir cómo escala el coste

El análisis **asintótico** describe cómo crece tiempo o memoria con \(n\). **O** cota superior, **Ω** inferior, **Θ** ajuste ajustado. Esta lección distingue **peor/promedio/mejor** caso y resuelve **recurrencias** típicas de divide y vencerás con **teorema maestro** (cuando aplica).

@section: Reglas prácticas

* Bucles anidados → multiplicación de límites.
* Dividir problema a la mitad → \(O(\log n)\) niveles si trabajo constante por nivel.

@section: Espacio auxiliar

Cuenta pilas de recursión y estructuras temporales.

@section: Errores frecuentes

* Ignorar constantes ocultas que importan en \(n\) pequeño.
* Confundir \(O(n^2)\) amortizado con peor caso.

@section: Laboratorio sugerido

1. Implementa búsqueda binaria y mide comparaciones vs lineal.
2. Resuelve \(T(n)=2T(n/2)+O(n)\).
3. Grafica tiempos reales de sort \(O(n\log n)\) vs burbuja.

@quiz: ¿Qué representa típicamente la notación O(...) en algoritmos?
@option: Tiempo exacto en milisegundos
@correct: Cota superior asintótica del crecimiento del recurso (tiempo o espacio)
@option: Solo el mejor caso
