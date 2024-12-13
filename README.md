# Instalación entorno virtual

A partir de la instalación de *Anaconda*
```
conda env create -f environment_mscandvc.yml
````
Luego para usar el entorno
```
conda activate mscandvc
```

# dvc_clasificacion_Arandanos

Datos y pipelines para clasificar vistas de olivas

# Para añadir remote webdav

```
dvc remote add -d rasp4remote webdavs://nube14.duckdns.org/remote.php/dav/files/dvcremote/datasets/olivas
dvc remote modify rasp4remote ssl_verify false
```

## Para introducir usuario y contraseña

Si se pone como se indica abajo, el usuario/contraseña NO se almacenan en el repositorio:

```
dvc remote modify --local rasp4remote user dvcremote
dvc remote modify --local rasp4remote password Cocentaina
```




______________________________________________________

# Procedimientos manuales

## Split Train-Val

```
 python ../src_clasificacion_vistas/data/MIL_split_train_val.py config/config1.yaml
```

## Para entrenar a mano

* El fichero generado está configurado en el yaml

* El fichero generado contiene información sobre fecha de creación, datos empleados, parámetros de entrenamiento,...


```
conda activate mscandvc
cd dvc_olivas_clasificacion_vistas
python ../src_clasificacion_vistas/train/train.py --config config/config1.yaml
```

### Guadar modelo en DVC

```
dvc add out_models/modelo.pkl
dvc push
```

Lo anterior actualiza/crea fichero out_models.pkl.dvc

Seguidamente deberemos hacer **git commit**

## Para evaluar a mano

Se lee el modelo y todas las imágenes de train y validación y se guardan sus scores.
El resultado se escribe en la carpeta out_evaluate (configurado en yaml)

También se calculan los aucs de cada tipo de defecto

```
conda activate mscandvc
cd dvc_olivas_clasificacion_vistas
python ../src_clasificacion_vistas/evaluate/evaluate.py --config config/config1.yaml
```

## Para generar report a mano a mano

Reejecuta todas las celdas de un cuaderno releyendo los ficheros con los scores de la última evaluación y actualiza el jupyter

```
conda activate mscandvc
cd dvc_olivas_clasificacion_vistas
jupyter nbconvert --to notebook --execute --inplace reports/analisis_prestaciones.ipynb
```

# Ejecución automática

Se pueden reproducir todos los pasos anteriores de un tirón, por ejemplo si se modifica la configuración al añadir una nueva carpeta de imágenes.

Se lee el fichero **dvc.yaml** , que és donde se describen todos los pasos:

* split train-val

* Entrenamiento

* Evaluación

* Reports


```
conda activate mscandvc
cd dvc_olivas_clasificacion_vistas
dvc repro
```

# Inferencia sobre imágenes no anotadas

Existe un script que recibe como entrada una lista de directorios y calcula los scores de cada defecto de cada imagen

De un fichero yaml de configuración se lee:

* Los patrones para encontrar imágenes 
* la configuración de donde se guarda el resultado
* El modelo a emplear (contiene información sobre la normalización)

```
python ../src_clasificacion_vistas/predict/predict.py config/config.yaml  directorio1 directorio2 ...'
```

