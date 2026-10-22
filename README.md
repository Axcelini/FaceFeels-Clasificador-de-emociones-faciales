FaceFeels: Clasificador de emociones faciales

Red neuronal convolucional (CNN) que reconoce 4 emociones (Angry, Happy, Sad, Neutral) a partir de imágenes de rostros, con una aplicación de escritorio para probarla.

Proyecto en equipo de la materia Inteligencia Artificial (Ingeniería en Desarrollo de Software, Universidad Tecmilenio, Campus San Nicolás, 2026).

Equipo: Axcel Francisco Salazar Alejo · Santiago Cano García · David Misael Martínez Moreno

Qué hace
Entrena una CNN con imágenes de rostros de 4 datasets públicos combinados (train.py).
Evalúa el modelo con imágenes que nunca vio: exactitud, precisión, recall, F1 y matriz de confusión.
Predice con una interfaz gráfica (app.py): eliges una foto, el programa detecta el rostro y muestra la emoción y la probabilidad de cada clase.

Formatos de imagen aceptados: JPG, PNG, BMP, WEBP y TIFF.

Resultados

Datos combinados: 60,662 imágenes.

Partición	Imágenes	Porcentaje
Entrenamiento	44,909	74%
Validación	7,923	13%
Prueba	13,042	13%
Exactitud en el conjunto de prueba: 77.35% (13,042 imágenes que no se usaron para entrenar).
Precisión por emoción: Happy 95%, Neutral 70%, Angry 69%, Sad 68%.
El modelo acierta más con emociones de rasgos muy marcados (Happy) y se confunde más entre Sad, Angry y Neutral, sobre todo cuando la expresión es sutil.
<!-- Cuando subas las imágenes de la carpeta modelo/, quita las marcas de comentario de estas dos líneas: ![Curvas de aprendizaje](modelo/curvas.png) ![Matriz de confusión](modelo/matriz_confusion.png) -->

Si vuelves a entrenar, los números pueden variar un poco: el entrenamiento tiene componentes aleatorios.

Datasets

El repositorio no incluye los datasets (pesan mucho). Descárgalos de Kaggle y colócalos en la carpeta raíz del proyecto, con estos nombres:

Dataset	Carpeta esperada
FER-2013	fer2013.zip (se extrae sola) o fer2013_data/
Face Expression Recognition Dataset	Face expression recognition dataset/
Facial Emotion Recognition Image Dataset	Facial Emotion Recognition Image Dataset/
CK+48	CK+48/

FER-2013: https://www.kaggle.com/datasets/msambare/fer2013 Los demás los puedes encontrar en Kaggle buscándolos por nombre.

train.py solo usa 4 emociones (angry, happy, sad, neutral) y mapea los nombres de cada dataset a esas clases (por ejemplo, anger → angry y sadness → sad). Las demás emociones se ignoran. Si falta algún dataset, el script avisa y continúa con los que encuentre.

Estructura del proyecto
FaceFeels/
├── train.py             ← entrena el modelo (se corre una sola vez)
├── app.py               ← interfaz gráfica
├── requirements.txt     ← dependencias
├── README.md
│
├── fer2013.zip / datasets…   ← los descargas tú (no están en el repo)
├── unified_data/             ← se crea sola al entrenar (datos combinados)
└── modelo/                   ← se crea sola al entrenar
    ├── emotion_model.keras
    ├── curvas.png
    └── matriz_confusion.png
Cómo usarlo
1. Crear y activar un entorno virtual
bash
python -m venv venv

Windows:

bash
venv\Scripts\activate

Mac / Linux:

bash
source venv/bin/activate
2. Instalar dependencias
bash
pip install -r requirements.txt

Si TensorFlow no instala, prueba con Python 3.10 a 3.12 o con pip install tensorflow-cpu.

3. Descargar los datasets

Colócalos en la carpeta raíz como se indica arriba.

4. Entrenar el modelo
bash
python train.py

En CPU tarda entre 30 y 60 minutos. Al terminar guarda el modelo en modelo/emotion_model.keras y genera las curvas de aprendizaje y la matriz de confusión. Si el repositorio ya incluye modelo/emotion_model.keras, puedes saltarte este paso.

5. Abrir la aplicación
bash
python app.py

Espera el aviso "✅ Modelo listo", elige una imagen con Seleccionar imagen y presiona Analizar emoción.

Cómo funciona

Datos

Se combinan los 4 datasets en unified_data/ con las mismas 4 clases.
Se eliminan las imágenes corruptas antes de entrenar.
Aumento de datos: rotación, desplazamiento, zoom, giro horizontal, cambios de brillo y shear.
Imágenes de 48×48 píxeles en escala de grises.

Modelo (CNN)

4 bloques convolucionales (32, 64, 128 y 256 filtros) con Batch Normalization, MaxPooling y Dropout.
Global Average Pooling y 2 capas densas (512 y 256) antes de la capa final softmax de 4 clases.

Entrenamiento

Optimizador AdamW (con weight decay) y label smoothing para reducir el sobreajuste.
Pesos por clase, porque algunas emociones tienen menos imágenes que otras.
Máximo 100 épocas, lotes de 64, con Early Stopping (paciencia de 20) y reducción automática de la tasa de aprendizaje.

Aplicación

Interfaz hecha con Tkinter.
Detecta rostros con OpenCV (Haar Cascade), toma el más grande y lo pasa al modelo.
Muestra la emoción detectada, la confianza y la probabilidad de cada una de las 4 emociones.
Limitaciones
Solo reconoce 4 emociones; no incluye sorpresa, miedo ni asco.
Funciona mejor con rostros de frente, bien iluminados y con expresiones claras. Con expresiones ambiguas baja la confianza y se confunde entre Sad, Angry y Neutral.
Los datasets son de acceso público y pueden tener sesgos de edad, género y origen, así que los resultados pueden variar entre personas.
Es un proyecto académico: no debe usarse para tomar decisiones sobre personas.
Tecnologías

Python · TensorFlow / Keras · OpenCV · Pillow · scikit-learn · Matplotlib · Seaborn · Tkinter
