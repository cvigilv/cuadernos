---
date: Sat Oct 17 14:13:30 -03 2020
tags: ['bugs' 'validacion cruzada' 'nbi']
---

# *Bug* validación cruzada ISCB-LA

Me di cuenta de un problema con el *pipeline* predictivo para NBI que utilice para calcular
las predicciones para el *poster* de ICSB-LA, los cuales invalidan la validación cruzada y
me benefician.

El *bug* corresponde a que al momento de hacer la validación cruzada, hay un paso donde se
genera una copia de la matriz de adyacencia `A` y de esta se eliminan las interacciones que
forman parte del set de prueba (*test set*). Al no eliminar estas uniones, esta información
aun forma parte del set de entrenamiento, provocando una sobreestimación del valor de
predicción de NBI.

En el código, el *bug* se ve de la siguiente manera:

```julia
for fold in 0:9
	# Create copy of adyacency matrix and remove the edges to predict
	A_fold = deepcopy(A)
	fold_edges = [e for (e,f) in fold_dict if f == fold]
	
	# Calculate recommendation matrix for fold and return the score for the 
	# edges in the test set
	R_fold = nbi(A_fold)*A_fold
	for (i,j) in fold_edges
		R[i,j] = R_fold[i,j]
	end
end
```

cuando deberia ser de la siguiente manera:

```julia
for fold in 0:9
	# Create copy of adyacency matrix and remove the edges to predict
	A_fold = deepcopy(A)
	fold_edges = [e for (e,f) in fold_dict if f == fold]

	# Check if edges can be removed and count the amount of edges deleted by target
	for (i,j) in fold_edges 
		A_fold[i,j] = 0
	end
	
	# Calculate recommendation matrix for fold and return the score for the 
	# edges in the test set
	R_fold = nbi(A_fold)*A_fold
	for (i,j) in fold_edges
		R[i,j] = R_fold[i,j]
	end
end
```

Como se ve en el segundo bloque de código, hay un paso adicional donde se eliminan las
uniones proteína-ligando de las uniones que forman parte del set de prueba del *fold*,
permitiendo una correcta predicción de las interacciones proteína-ligando con el método de
NBI. Los resultados obtenidos para las predicciones con esa versión de NBI son las
siguientes:

| Set de datos     | ROC (AUC)    | PR (AUC)     |
|:-----------------|:------------:|:------------:|
| Enzyme	       | 0.89         | 0.77         |
| Ion Channel      | No calculado | No calculado |
| GPCR		       | 0.81         | 0.61         |
| Nuclear Receptor | 0.83         | 0.61         |

Ante esto, se decide correr nuevamente las predicciones para los sets de datos *gold
standard* de Yamanishi et al. (2008) dando los siguientes valores:

| Set de datos     | ROC (AUC) | PR (AUC) |
|:-----------------|:---------:|:--------:|
| Enzyme	       | 0.89      | 0.77     |
| Ion Channel      | 0.93      | 0.83     |
| GPCR		       | 0.84      | 0.62     |
| Nuclear Receptor | 0.68      | 0.39     |
Table: Rendimiento NBI en sets de datos *gold standard* propuestos por Yamanishi et al.

Por último, se decide reproducir el *bug*, con la finalidad de estar 100% seguro de que este
era el problema que presentaba el *script* de predicción. Para eso, se volvió a correr el
*script* predictivo, pero con una versión de la función `split_adjmat` que no borra las
interacciones *u <-> o* del set de entrenamiento, obteniendo los siguientes resultados:


