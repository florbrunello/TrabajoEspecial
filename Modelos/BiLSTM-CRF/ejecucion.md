1. Descargar e inicializar los repositorios de los modelos:
git clone --recurse-submodules "https://github.com/JulietaStorino/Text-Mining-Proyect-FaMAF.git"
cd Text-Mining-Proyect-FaMAF/SPACCC_MEDDOCAN
git clone "https://github.com/PlanTL-GOB-ES/SPACCC_MEDDOCAN.git" .
cd ..

2. Descomprimir y reestructurar los datos de entrenamiento:
[Añadir el dataset correspondiente]
cd models/BiLSTM-CRF
unzip data.zip
mv data/train .
mv data/dev .
mkdir output
mv data/test output

3. Crear el entorno virtual:
python3.7 -m venv .env
source .env/bin/activate

4. Instalar las dependencias:
pip install tensorflow==1.14.0 numpy==1.16.4 scipy==1.3.0 cython wheel spacy==2.3.2 nltk matplotlib gast==0.2.2 scikit-learn
pip install protobuf==3.20.3

5. Descargar pipeline en español optimizado para CPU:
python3 -m spacy download es_core_news_sm

6. Reemplaza todas las instancias de spacy.load('es') por spacy.load('es_core_news_sm'):
cd code
sed -i "s/spacy.load('es')/spacy.load('es_core_news_sm')/g" preprocessing.py

7. Descargar los recursos necesarios:
python3 ../../../requirements.py

8. Preprocesar los datos:
[Eliminar los anteriores]
python3 preprocessing.py --dataDir ../train/gold --train
python3 preprocessing.py --dataDir ../dev/gold --dev
python3 preprocessing.py --dataDir ../output/test/gold --test

9. Descarga el embedding de palabras necesario:
cd Extension2
wget -O wiki.es.vec https://dl.fbaipublicfiles.com/fasttext/vectors-wiki/wiki.es.vec

10. Genera los archivos necesarios por el modelo neuronal:
[Eliminar los anteriores]
python3 create_vocabs.py --trainpickle ../train_word_ner_startidx_dict.pickle --devpickle ../dev_word_ner_startidx_dict.pickle --testpickle ../test_word_ner_startidx_dict.pickle --embfile wiki.es.vec --vocabEmbFile vocab_embeddings.npz

11. Crear directorios para guardar el modelo y los confusion plots:
mkdir Model
mkdir plots
cd Code

12. Correr el modelo:
python3 train.py

13. Evaluar el modelo con los datos del MEDDOCAN:
cd ../..
python3 evaluate.py brat ner ../output/test/gold ../output/test/system

Del tipo IDENTF_BIOMETRICOS hay 0 en MEDDOCAN -> que hacer ? 