# DeepFaceLab_Demo

## Instalacion
### Advertensia
Se requieren al menos 16 GB de memoria RAM para hacer uso correcto de la herramienta.

Descargue la compilación correcta de DFL para su GPU (el esquema de nombres de compilación puede cambiar):

- para las series Nvidia GTX 900-1000 y RTX 2000 y otras GPU que utilizan las mismas arquitecturas que esas series, use la compilación "DeepFaceLab_NVIDIA_up_to_RTX2080Ti" .
- para las tarjetas de la serie Nvidia RTX 3000-4000 y otras GPU que utilizan las mismas arquitecturas, use la compilación "DeepFaceLab_NVIDIA_RTX3000_series" .
- para las tarjetas AMD modernas, use la compilación "DeepFaceLab_DirectX12" (es posible que no funcione en algunas GPU AMD más antiguas).

(Enlace de descarga en MEGA)[https://mega.nz/folder/Po0nGQrA#dbbttiNWojCt8jzD4xYaPw]


# Conceptos Basicos

SRC: siempre se refiere al contenido (cuadros, caras) de la persona cuya cara estamos tratando de intercambiar en un video o foto de destino.

Conjunto SRC/Conjunto de datos SRC/Conjunto de datos de origen/Caras SRC: caras extraídas (archivo de imagen de proporción cuadrada de la cara de origen que contiene datos adicionales como puntos de referencia, máscaras, etiquetas xseg, posición/tamaño/rotación en el marco original) de la persona que estamos probando para cambiar a un video.

DST: siempre se refiere al contenido (fotogramas, rostros) del video de destino (o video DST/DST) en el que estamos intercambiando rostros.

Conjunto de DST/Conjunto de datos de DST/Conjunto de datos de destino/Caras de DST: colección de rostros extraídos de la persona objetivo cuyos rostros reemplazaremos con una semejanza de SRC, el mismo formato y contiene el mismo tipo de datos que los rostros de SRC.

Fotogramas: fotogramas extraídos de videos de origen o de destino, después de la extracción de los fotogramas, se colocan dentro de las carpetas "data_src" o "data_dst", respectivamente.

Caras: imágenes SRC/DST de caras extraídas de fotogramas originales derivados de videos o fotos utilizados.

Modelo: colección de archivos que conforman los modelos SAEHD, AMP y XSeg que el usuario puede crear/entrenar, todos se colocan dentro de la carpeta "modelo" que se encuentra dentro de la carpeta "espacio de trabajo", modelos de descripción básica a continuación (más detallados más adelante en la guía ):

1. SAEHD: el modelo más popular y más utilizado, viene en varias variantes diferentes basadas en varias arquitecturas, cada una con sus propias ventajas y desventajas; sin embargo, en general, está destinado a intercambiar caras cuando tanto SRC como DST comparten algunas similitudes, particularmente generales forma de cara/cabeza. Puede reutilizarse libremente, preentrenarse y, en general, puede ofrecer resultados rápidos con una calidad decente, pero algunas arquitecturas pueden sufrir de baja semejanza o mala iluminación y combinación de colores.

2. AMP: nuevo modelo experimental que puede adaptarse más a los datos de origen y conservar su forma, lo que significa que puede usarlo para intercambiar caras que no se parecen en nada; sin embargo, esto requiere una composición manual, ya que DFL no tiene técnicas de enmascaramiento más avanzadas, como la pintura de fondo. . A diferencia de SAEHD, no tiene diferentes arquitecturas para elegir y es menos versátil cuando se trata de reutilización y lleva mucho más tiempo entrenar, tampoco tiene opción de preentrenamiento pero puede ofrecer una calidad mucho mayor y los resultados pueden parecerse más a SRC.

3. Quick 96: modelo de prueba, utiliza parámetros de resolución SAEHD DF-UD 96 y tipo de cara Full Face, destinados a pruebas rápidas.

4. XSeg: modelo de enmascaramiento entrenable por el usuario que se usa para generar máscaras más precisas para caras SRC y DST que pueden excluir varias obstrucciones (dependiendo de las etiquetas de los usuarios en las caras SRC y DST), DFL viene con un modelo genérico de enmascaramiento facial entrenado que puede usar si no desea crear sus propias etiquetas de inmediato.

Etiquetas XSeg: etiquetas creadas por el usuario en el editor XSeg que definen las formas de las caras, también pueden incluir exclusiones (o no incluir en primer lugar) obstrucciones sobre las caras SRC y DST, utilizadas para entrenar el modelo XSeg para generar máscaras.

Máscaras: generadas por el modelo XSeg, las máscaras son necesarias para definir áreas de la cara que se supone que deben entrenarse (ya sean caras SRC o DST), así como para definir la forma y las obstrucciones necesarias para el enmascaramiento final durante la fusión (DST). Una forma de máscaras básicas también está incrustada en las caras extraídas de forma predeterminada que se deriva de puntos de referencia faciales, es una máscara básica que se puede usar para hacer intercambios básicos usando modelos de tipo de cara completa o inferior (más sobre tipos de cara y máscaras más adelante en el guía)


# 1. LIMPIEZA/ELIMINACIÓN DEL ESPACIO DE TRABAJO
Elimina todos los datos de la carpeta "espacio de trabajo", hay algunos archivos de demostración predeterminados en la carpeta "espacio de trabajo" cuando descarga una nueva versión de DFL que puede usar para practicar su primera falsificación, puede eliminarlos al mano o use este .bat para borrar su carpeta "área de trabajo", pero como rara vez solo elimina modelos y conjuntos de datos después de terminar de trabajar en un proyecto, este .bat es básicamente inútil y peligroso ya que puede eliminar accidentalmente todo su trabajo, por lo que lo recomiendo. eliminar este .bat.

# 2. RECOPILACIÓN Y EXTRACCIÓN DEL CONTENIDO FUENTE
Puede extraer marcos de diferentes maneras:

a) extrae cada video por separado cambiando el nombre de cada uno a data_src (el video debe estar en formato mp4 pero DFL usa FFMPEG, por lo que potencialmente debería manejar cualquier formato y códec) usando 2) Extraer imágenes del video data_src para extraer fotogramas del archivo de video, después de lo cual se envían a la carpeta "data_src" (se crea automáticamente), opciones disponibles:

- FPS: omita la velocidad de fotogramas predeterminada de los videos, ingrese el valor numérico para otra velocidad de fotogramas (por ejemplo, ingresar 5 solo representará el video como 5 fotogramas) por segundo, lo que significa que se extraerán menos fotogramas), dependiendo de la longitud, recomiendo 5-10FPS para la extracción de fotogramas SRC, independientemente de cómo extraiga sus fotogramas (método b y c)

- JPG/PNG: elija el formato de los fotogramas extraídos, los jpg son más pequeños y tienen una calidad ligeramente inferior, los png son más grandes pero los fotogramas extraídos tienen una mejor calidad, no debería haber pérdida de calidad con PNG en comparación con el video original.

b) importa todos los videos a un software de edición de video de su elección, asegurándose de no editar videos de diferentes resoluciones juntos, sino que procesa videos de 720p, 1080p, 4K por separado, en este punto también puede cortar videos para mantener solo las mejores tomas que tienen rostros de la mejor calidad, es decir, tomas en las que los rostros están muy lejos/pequeños, están borrosos (fuera de foco, desenfoque de movimiento severo), son muy oscuros o iluminados con iluminación de un solo color o simplemente que la iluminación no es muy natural o tiene partes muy brillantes y sombras oscuras al mismo tiempo, así como las tomas en las que la mayoría de la cara está obstruida deben descartarse a menos que sea una expresión muy singular que no ocurre con frecuencia o está en un ángulo que también rara vez se encuentra ( como personas que miran directamente hacia arriba o hacia abajo) o si su video de destino realmente tiene una iluminación tan estilizada,a veces solo tiene estas caras de menor calidad si no puede encontrar el ángulo dado en ningún otro lugar, luego renderice los videos directamente en marcos jpg o png en su carpeta data_src (créelo manualmente si lo eliminó antes) y renderice el lote completo de videos a una resolución determinada o renderizar cada clip por separado.

# 3. EXTRACCIÓN DE FOTOS DEL VIDEO DE DESTINO (DATA_DST.MP4)
También necesita extraer fotogramas de su video de destino, después de editarlo de la forma que desea, renderícelo como data_dst.mp4 y extraiga fotogramas usando 3) extraiga imágenes del video data_dst FULL FPS , los fotogramas se colocarán en "data_dst " carpeta, las opciones disponibles son salida en formato JPG o PNG: seleccione JPG si desea un tamaño más pequeño, PNG para obtener la mejor calidad. No hay una opción de velocidad de fotogramas porque desea extraer el video a la velocidad de fotogramas original.

# 4. EXTRACCIÓN DE FACE/DATASET DE DATA_SRC
La segunda etapa de la preparación del conjunto de datos SRC es extraer caras de los marcos extraídos ubicados dentro de la carpeta "data_src". Suponiendo que cambió el nombre de todos los conjuntos de marcos dentro de sus carpetas, muévalos nuevamente a la carpeta principal "data_src" y ejecute lo siguiente 4) extracto de faceset data_src : extractor automatizado que usa el algoritmo S3FD, esto manejará la mayoría de las caras en su conjunto pero no es perfecto, no detectará algunas caras y producirá muchos falsos positivos y detectará otras personas que tendrás que eliminar más o menos manualmente.

También hay 4) extracto de faceset data_src MANUAL- extractor manual, ver 5.1 para el uso. Puede usarlo para alinear manualmente algunas caras, especialmente si tiene algunas imágenes o una fuente pequeña de películas que presentan algunos ángulos raros que tienden a ser difíciles para el extractor automático (como mirar directamente hacia arriba o hacia abajo).

Las opciones disponibles para el extractor S3FD y MANUAL son:

- Qué GPU (o CPU) usar para la extracción : use GPU, casi siempre es más rápido.

- Tipo de cara:

a) Cara completa/FF: para modelos FF o tipos de cara inferiores (Half Face/Hf y Mid-Half Face/MF, rara vez se usan en la actualidad).

b) Rostro completo/WF: para modelos WF o inferiores, recomendado como una solución universal/preparada para el futuro para trabajar con modelos FF y WF.

c) Cabeza: para los modelos HEAD, puede funcionar con otros modelos como WF o FF, pero requiere una resolución de extracción muy alta para que las caras tengan el mismo nivel de detalle (nitidez) que los tipos de cara de menor cobertura, utiliza puntos de referencia 3D en lugar de 2D como en FF y WF, pero aún es compatible con modelos que usan esos tipos de cara.

Recuerda que siempre puedes cambiar el tipo de cara (y su resolución) para bajar uno más tarde usando 4.2) data_src/dst util faceset resizeo MVE (también puede convertir el conjunto de resolución/tipo de cara más bajo en uno más alto, pero requiere que conserve los marcos y las fotos originales). Por lo tanto, recomiendo usar WF si realiza intercambios de rostros principalmente con modelos FF y WF y HEAD para conjuntos de pelo corto que se usan principalmente para intercambios de HEAD, pero también los que puede querer usar en algún momento para intercambios de rostros FF/WF.

- Número máximo de caras de la imagen : cuántas caras debe extraer el extractor de un cuadro, 0 es el valor recomendado, ya que extrae todas las que puede encontrar. Seleccionar 1 o 2 solo extraerá 1 o 2 caras de cada cuadro.

- Resolución del conjunto de datos:Este valor dependerá en gran medida de la resolución de los fotogramas de origen, a continuación se encuentran mis recomendaciones personales según la resolución del clip de origen, por supuesto, puede usar diferentes valores, incluso puede medir qué tan grande es la cara más grande en una fuente dada y usar eso como un valor (recuerde usar valores en incrementos de 16).

La resolución también se puede cambiar más adelante usando 4.2) data_src/dst util faceset resize o MVE, incluso puede usar MVE para extraer rostros con la opción de tamaño de rostro estimado que usará datos de puntos de referencia de sus rostros extraídos, marcos originales y volverá a extraer todo su establezca de nuevo el tamaño real de cada cara en los marcos. Puede leer más sobre cómo cambiar tipos de cara, resoluciones de conjuntos de datos y más en esos dos subprocesos de guías de MVE:

https://mrdeepfakes.com/forums/thread-how-to-fix-face-landmarks-with-mve

https://mrdeepfakes.com/forums/thread-mve-machine-video-editor-guide

Recomiendo los siguientes valores para WF: fuentes de resolución de

720p o inferior: 512-768
fuentes de 1080p: 768-1024
fuentes de 4K: 1024-2048

tamaño real de la cabeza en un marco donde está más cerca de la cámara. En caso de duda, utilice MVE para extraer rostros con la opción de tamaño de rostro estimado habilitada.

- calidad jpeg- utilice 100 para obtener la mejor calidad. DFL solo puede extraer rostros en formato JPG. No hay razón para ir por debajo de 100, la diferencia de tamaño no será grande, pero la calidad disminuirá drásticamente, lo que resultará en una peor calidad.

- Elegir si generar imágenes "aligned_debug" o no : se puede generar después, se usan para verificar si los puntos de referencia son correctos; sin embargo, esto también se puede hacer con MVE y en realidad puede corregir manualmente los puntos de referencia con MVE, por lo que en la mayoría de los casos esto es no es muy útil para conjuntos de datos SRC.


# 5. Fusión
Una vez que haya terminado de entrenar su modelo, es hora de fusionar la cara aprendida sobre los marcos originales para formar el video final.

Para eso tenemos 3 convertidores correspondientes a 3 modelos disponibles:

7) fusionar SAEHD
7) fusionar AMP
7) fusionar Quick96

Al seleccionar cualquiera de ellos, aparecerá una ventana de línea de comando con varias indicaciones.
El primero le preguntará si desea usar un convertidor interactivo, el valor predeterminado es y (habilitado) y se recomienda usarlo sobre el normal porque tiene todas las funciones y también una vista previa interactiva donde puede ver los efectos de todos los cambios haces cuando cambias varias opciones y habilitas/deshabilitas varias funciones
¿Usar combinación interactiva? (t/n):

El segundo le preguntará qué modelo desea usar:
elija uno de los modelos guardados o ingrese un nombre para crear un nuevo modelo.
[r]: renombrar
[d]: eliminar
[0]: df192 -

el tercero más reciente le preguntará qué GPU/GPU o CPU desea usar para el proceso de fusión (conversión):
elija uno o varios idx de GPU (separados por coma ).
[CPU] : CPU
[0] : Tu GPU
[0] ¿Qué índices de GPU elegir? :

Al presionar enter se usará el valor predeterminado (0).