# Sonido estéreo y ficheros WAVE

## Nom i cognoms: Gabriel Lorenzo Felix

## El formato WAVE

El formato WAVE es uno de los más extendidos para el almacenamiento y transmisión
de señales de audio. En el fondo, se trata de un tipo particular de fichero
[RIFF](https://en.wikipedia.org/wiki/Resource_Interchange_File_Format) (*Resource
Interchange File Format*), utilizado no sólo para señales de audio sino también para señales de
otros tipos, como las imágenes estáticas o en movimiento, o secuencias MIDI.

La base de los ficheros RIFF es el uso de *cachos* (*chunks*, en inglés). Cada cacho,
o subcacho, está encabezado por una cadena de cuatro caracteres ASCII, que indica el tipo del cacho,
seguido por un entero sin signo de cuatro bytes, que indica el tamaño en bytes de lo que queda de
cacho sin contar la cadena inicial y el propio tamaño. A continuación, y en función del tipo de
cacho, se colocan los datos que lo forman.

Todo fichero RIFF incluye un primer cacho que lo identifica como tal y que empieza por la cadena
`'RIFF'`. A continuación, después del tamaño del cacho y en otra cadena de cuatro caracteres,
se indica el tipo concreto de información que contiene el fichero. En el caso concreto de los
ficheros de audio WAVE, esta cadena es igual a `'WAVE'`, y el cacho debe contener dos
*subcachos*: el primero, de nombre `'fmt '`, proporciona la información de cómo está
codificada la señal. Por ejemplo, si es PCM lineal, ADPCM, etc., o si es monofónica o estéreo. El
segundo subcacho, de nombre `'data'`, incluye las muestras de la señal.

Dispone de una descripción detallada del formato WAVE en la página
[WAVE PCM soundfile format](http://soundfile.sapp.org/doc/WaveFormat/) de Soundfile.

## Audio estéreo

La mayor parte de los animales, incluidos los del género *homo sapiens sapiens* sanos y completos,
están dotados de dos órganos que actúan como transductores acústico-sensoriales (es decir, tienen dos
*oídos*). Esta duplicidad orgánica permite al bicho, entre otras cosas, determinar la dirección de
origen del sonido. En el caso de la señal de música, además, la duplicidad proporciona una sensación
de *amplitud espacial*, de realismo y de confort acústico.

En un principio, los equipos de reproducción de audio no tenían en cuenta estos efectos y sólo permitían
almacenar y reproducir una única señal para los dos oídos. Es el llamado *sonido monofónico* o
*monoaural*. Una alternativa al sonido monofónico es el *estereofónico* o, simplemente, *estéreo*. En
él, se usan dos señales independientes, destinadas a ser reproducidas a ambos lados del oyente: los
llamados *canal izquierdo* (**L**) y *derecho* (**R**).

Aunque los primeros experimentos con sonido estereofónico datan de finales del siglo XIX, los primeros
equipos y grabaciones de este tipo no se popularizaron hasta los años 1950 y 1960. En aquel tiempo, la
gestión de los dos canales era muy rudimentaria. Por ejemplo, los instrumentos se repartían entre los
dos canales, con unos sonando exclusivamente a la izquierda, y el resto a la derecha. Es el caso de las
primeras grabaciones en estéreo de los Beatles: las versiones en alemán de los singles *She loves you*
y *I want to hold your hand*. Así, en esta última (de la que dispone de un fichero en Atenea con sus
primeros treinta segundos, [Komm, gib mir deine Hand](wav/komm.wav)), la mayor parte de los instrumentos
suenan por el canal derecho, mientras que las voces y las características palmas lo hacen por el izquierdo.

Un problema habitual en los primeros años del sonido estereofónico, y aún vigente hoy en día, es que no
todos los equipos son capaces de reproducir los dos canales por separado. La solución comúnmente
adoptada consiste en no almacenar cada canal por separado, sino en la forma semisuma, $(L+R)/2$, y
semidiferencia, $(L-R)/2$, y de tal modo que los equipos monofónicos sólo accedan a la primera de ellas.
De este modo, estos equipos pueden reproducir una señal completa, formada por la suma de los dos
canales, y los estereofónicos pueden reconstruir los dos canales estéreo.

Por ejemplo, en la radio FM estéreo, la señal, de ancho de banda 15 kHz, se transmite del modo siguiente:

- En banda base, $0\le f\le 15$ kHz, se transmite la suma de los dos canales, $L+R$. Esta es la señal
  que son capaces de reproducir los equipos monofónicos.

- La señal diferencia, $L-R$, se transmite modulada en amplitud con una frecuencia de portadora
  $f_m = 38$ kHz.

  - Por tanto, ocupa la banda $23 \mathrm{kHz}\le f\le 53 \mathrm{kHz}$, que sólo es accedida por los
    equipos estéreo, y, en el caso de colarse en un reproductor monofónico, ocupa la banda no audible.

- También se emite una sinusoide de $19 \mathrm{kHz}$, denominada *señal piloto*, que se usa para
  demodular síncronamente la señal diferencia.

- Finalmente, la señal de audio estéreo puede acompañarse de otras señales de señalización y servicio en
  frecuencias entre $55.35 \mathrm{kHz}$ y $94 \mathrm{kHz}$.

En los discos fonográficos, la semisuma de las señales está grabada del mismo modo que se haría en una
grabación monofónica, es decir, en la profundidad del surco; mientras que la semidiferencia se graba en el
desplazamiento a izquierda y derecha de la aguja. El resultado es que un reproductor mono, que sólo atiende
a la profundidad del surco, reproduce casi correctamente la señal monofónica, mientras que un reproductor
estéreo es capaz de separar los dos canales. Es posible que algo de la información de la semisuma se cuele
en el reproductor mono, pero, como su amplitud es muy pequeña, se manifestará como un ruido muy débil,
apenas perceptible.

En general, todos estos sistemas se basan en garantizar que el reproductor mono recibe correctamente la
semisuma de canales y que, si algo de la semidiferencia se cuela en la reproducción, sea en forma de un
ruido inaudible.

## Tareas a realizar

Escriba el fichero `estereo.py` que incluirá las funciones que permitirán el manejo de los canales de una
señal estéreo y su codificación/decodificación para compatibilizar ésta con sistemas monofónicos.

### Manejo de los canales de una señal estéreo

En un fichero WAVE estéreo con señales de 16 bits, cada muestra de cada canal se codifica con un entero de
dos bytes. La señal se almacena en el *cacho* `'data'` alternando, para cada muestra de $x[n]$, el valor
del canal izquierdo y el derecho:

<img src="img/est%C3%A9reo.png" width="380px">

#### Función `estereo2mono(ficEste, ficMono, canal=2)`

La función lee el fichero `ficEste`, que debe contener una señal estéreo, y escribe el fichero `ficMono`,
con una señal monofónica. El tipo concreto de señal que se almacenará en `ficMono` depende del argumento
`canal`:

- `canal=0`: Se almacena el canal izquierdo $L$.
- `canal=1`: Se almacena el canal derecho $R$.
- `canal=2`: Se almacena la semisuma $(L+R)/2$. Es la opción por defecto.
- `canal=3`: Se almacena la semidiferencia $(L-R)/2$.

#### Función `mono2estereo(ficIzq, ficDer, ficEste)`

Lee los ficheros `ficIzq` y `ficDer`, que contienen las señales monofónicas correspondientes a los canales
izquierdo y derecho, respectivamente, y construye con ellas una señal estéreo que almacena en el fichero
`ficEste`.

### Codificación estéreo usando los bits menos significativos

En la línea de los sistemas usados para codificar la información estéreo en señales de radio FM o en los
surcos de los discos fonográficos, podemos usar enteros de 32 bits para almacenar los dos canales de 16 bits:

- En los 16 bits más significativos se almacena la semisuma de los dos canales.

- En los 16 bits menos significativos se almacena la semidiferencia.

Los sistemas monofónicos sólo son capaces de manejar la señal de 32 bits. Esta señal es prácticamente
idéntica a la señal semisuma, ya que la semisuma ocupa los 16 bits más significativos. La señal
semidiferencia aparece como un ruido añadido a la señal, pero, como su amplitud es $2^{16}$ veces más
pequeña, será prácticamente inaudible (la relación señal a ruido es del orden de 90 dB).

Los sistemas estéreo son capaces de aislar las dos partes de la señal y, con ellas, reconstruir los dos
canales izquierdo y derecho.

<img src="img/est%C3%A9reo_cod.png" width="510px">

#### Función `codEstereo(ficEste, ficCod)`

Lee el fichero \python{ficEste}, que contiene una señal estéreo codificada con PCM lineal de 16 bits, y
construye con ellas una señal codificada con 32 bits que permita su reproducción tanto por sistemas
monofónicos como por sistemas estéreo preparados para ello.

#### Función `decEstereo(ficCod, ficEste)`

Lee el fichero \python{ficCod} con una señal monofónica de 32 bits en la que los 16 bits más significativos
contienen la semisuma de los dos canales de una señal estéreo y los 16 bits menos significativos la
semidiferencia, y escribe el fichero \python{ficEste} con los dos canales por separado en el formato de los
ficheros WAVE estéreo.

### Entrega

#### Fichero `estereo.py`

- El fichero debe incluir una cadena de documentación que incluirá el nombre del alumno y una descripción
  del contenido del fichero.

- Es muy recomendable escribir, además, sendas funciones que *empaqueten* y *desempaqueten* las cabeceras
  de los ficheros WAVE a partir de los datos contenidos en ellas.

- Aparte de `struct`, no se puede importar o usar ningún módulo externo.

- Se deben evitar los bucles. Se valorará el uso, cuando sea necesario, de *comprensiones*.

- Los ficheros se deben abrir y cerrar usando gestores de contexto.

- Las funciones deberán comprobar que los ficheros de entrada tienen el formato correcto y, en caso
  contrario, elevar la excepción correspondiente.

- Los ficheros resultantes deben ser reproducibles correctamente usando cualquier reproductor estándar;
  por ejemplo, el Windows Media Player o similar. Es probable, muy probable, que tenga que modificar los  datos de las cabeceras de los ficheros para conseguirlo.

- Se valorará lo pythónico de la solución; en concreto, su claridad y sencillez, y el uso de los estándares
  marcados por PEP-ocho.

#### Comprobación del funcionamiento

Es responsabilidad del alumno comprobar que las distintas funciones realizan su cometido de manera correcta.
Para ello, se recomienda usar la canción [Komm, gib mir deine Hand](wav/komm.wav), suminstrada al efecto.
De todos modos, recuerde que, aunque sea en alemán, se trata de los Beatles, así que procure no destrozar
innecesariamente la canción.

#### Código desarrollado

Inserte a continuación el código de los métodos desarrollados en esta tarea, usando los comandos necesarios
para que se realice el realce sintáctico en Python del mismo (no vale insertar una imagen o una captura de
pantalla, debe hacerse en formato *markdown*).

##### Código de `estereo2mono()`

##### Código de `mono2estereo()`

##### Código de `codEstereo()`

##### Código de `decEstereo()`

```python
import struct

def leeWave(fitWave):
    '''
    Lectura del fichero fitWave
    '''
    with open(fitWave, "rb") as fpWave:
        capcelera = '<4sI4s'
        chunkID, chunkSize, formato = struct.unpack(capcelera, fpWave.read(struct.calcsize(capcelera)))
        if chunkID != b'RIFF' or formato != b'WAVE':
            raise Exception('El fichero no tiene formato wave')

        fmtCap = '<4sI2H2I2H'
        (subChunk1ID, subChunk1Size, audioFormat, numChannels,
         sampleRate, byteRate, blockAlign, bitsPerSample) = struct.unpack(fmtCap, fpWave.read(struct.calcsize(fmtCap)))

        fmtData = '<4sI'
        subChunk2ID, subChunk2Size = struct.unpack(fmtData, fpWave.read(struct.calcsize(fmtData)))
        data = fpWave.read(subChunk2Size)

        bytesPerSample = bitsPerSample // 8
        fmtSample = 'h' if bytesPerSample == 2 else 'h'  # Use 'h' for both 16-bit and 32-bit samples

        if numChannels == 1:
            numSamples = subChunk2Size // bytesPerSample
            fmtSen = '<' + str(numSamples) + fmtSample
            senyal = [struct.unpack(fmtSen, data)]

        elif numChannels == 2:
            numSamples = subChunk2Size // (bytesPerSample * 2)
            fmtSen = '<' + str(numSamples * 2) + fmtSample
            senyal = struct.unpack(fmtSen, data)
            senyal = [senyal[::2], senyal[1::2]]

    return senyal, sampleRate

def writeWave(fileWave, signal, sampleRate):
    '''
    Escritura del fichero 
    '''
    with open(fileWave, 'wb') as fpWave:
        chunkSize = 44 + 2 * len(signal[0]) + 2 * (len(signal[1]) if len(signal) > 1 else 0)
        fpWave.write(struct.pack('<4sI4s', b'RIFF', chunkSize, b'WAVE'))

        fpWave.write(struct.pack('<4sI2H2I2H', b'fmt ', 16, 1, len(signal), sampleRate, 16 // 8 * sampleRate * len(signal), len(signal) * 16 // 8, 16))

        numMuestras = len(signal[0]) + (len(signal[1]) if len(signal) > 1 else 0)
        fpWave.write(struct.pack('<4sI', b'data', 2 * numMuestras))

        fmtSen = '<' + str(numMuestras) + 'h'

        if len(signal) == 1:
            fpWave.write(struct.pack(fmtSen, *signal[0]))

        elif len(signal) == 2:
            sen = [None] * (len(signal[0]) + len(signal[1]))
            sen[::2] = signal[0]
            sen[1::2] = signal[1]
            fpWave.write(struct.pack(fmtSen, *sen))

## 1: De estereo a mono
def estereo2mono(ficEste, ficMono, canal=2):
    '''
    La función lee el fichero ficEste, que debe contener una señal estéreo, y escribe el fichero ficMono,
    con una señal monofónica. El tipo concreto de señal que se almacenará en ficMono depende del argumento
    canal:
    - canal=0: Se almacena el canal izquierdo L.
    - canal=1: Se almacena el canal derecho R.
    - canal=2: Se almacena la semisuma (L+R)/2. Es la opción por defecto.
    - canal=3: Se almacena la semidiferencia (L-R)/2.
    '''
    signal, sampleRate = leeWave(ficEste)
    
    if canal == 0:
        writeWave(ficMono, [signal[0]], sampleRate)
    elif canal == 1:
        writeWave(ficMono, [signal[1]], sampleRate)
    elif canal == 2:
        semisuma = [(v1 + v2) // 2 for v1, v2 in zip(signal[0], signal[1])]
        writeWave(ficMono, [semisuma], sampleRate)
    elif canal == 3:
        semidiferencia = [(v1 - v2) // 2 for v1, v2 in zip(signal[0], signal[1])]
        writeWave(ficMono, [semidiferencia], sampleRate)


## 2: De mono a estereo
def mono2estereo(ficIzq, ficDer, ficEste):
        
    '''
    Lee los ficheros ficIzq y ficDer, que contienen las señales monofónicas correspondientes a los canales
    izquierdo y derecho, respectivamente, y construye con ellas una señal estéreo que almacena en el fichero
    ficEste.
    '''

    signalIzq, sampleRate = leeWave(ficIzq)
    signalDer, sampleRate = leeWave(ficDer)
    writeWave(ficEste, [*signalIzq, *signalDer], sampleRate)


## 3: Codificador estereo
def codEstereo(ficEste, ficCod):
    '''
    Lee el fichero \python{ficEste}, que contiene una señal estéreo codificada con PCM lineal de 16 bits, y
    construye con ellas una señal codificada con 32 bits que permita su reproducción tanto por sistemas
    monofónicos como por sistemas estéreo preparados para ello.
    '''
    signal, sampleRate = leeWave(ficEste)
    
    with open(ficCod, 'wb') as fpWave:
        chunkSize = 44 + 32 // 8 * len(signal[0]) + 32 // 8 * (len(signal[1]) if len(signal) > 1 else 0)
        fmtRiff = '<4sI4s'
        fpWave.write(struct.pack(fmtRiff, b'RIFF', chunkSize, b'WAVE'))
        
        fmtCap = '<4sI2H2I2H'
        fpWave.write(struct.pack(fmtCap, b'fmt ', 16, 1, 1, sampleRate, 32 // 8 * sampleRate * 1, 1 * 32 // 8, 32)) #1:audioformat fmlineal
        
        fmtData = '<4sI'
        numMuestras = len(signal[0]) + (len(signal[1]) if len(signal) > 1 else 0)
        fpWave.write(struct.pack(fmtData, b'data', 32 // 8 * numMuestras))
        
        fmtSen = '<' + str(numMuestras) + 'h'
        
        semisuma = [(v1 + v2) // 2 for v1, v2 in zip(signal[0], signal[1])]
        semidiferencia = [(v1 - v2) // 2 for v1, v2 in zip(signal[0], signal[1])]
        
        sen = [None] * (len(semisuma) + len(semidiferencia))
        sen[::2] = semisuma
        sen[1::2] = semidiferencia
        
        fpWave.write(struct.pack(fmtSen, *sen))


## 4: Descodificador estereo
def decEstereo(ficCod, ficEste):
    '''
    Lee el fichero \python{ficCod} con una señal monofónica de 32 bits en la que los 16 bits más significativos
    contienen la semisuma de los dos canales de una señal estéreo y los 16 bits menos significativos la
    semidiferencia, y escribe el fichero \python{ficEste} con los dos canales por separado en el formato de los
    ficheros WAVE estéreo.
    '''
    signal, sampleRate = leeWave(ficCod)
    
    with open(ficEste, 'wb') as fpWave:
        chunkSize = 44 + 2 * len(signal[0]) + 2 * (len(signal[1]) if len(signal) > 1 else 0)
        fmtRiff = '<4sI4s'
        fpWave.write(struct.pack(fmtRiff, b'RIFF', chunkSize, b'WAVE'))
        
        fmtCap = '<4sI2H2I2H'
        fpWave.write(struct.pack(fmtCap, b'fmt ', 16, 1, 2, sampleRate, 16 // 8 * sampleRate * 2, 2 * 16 // 8, 16)) #1:audioformat fmlineal
        
        fmtData = '<4sI'
        numMuestras = len(signal[0]) + (len(signal[1]) if len(signal) > 1 else 0)
        fpWave.write(struct.pack(fmtData, b'data', 2 * numMuestras))
                       
        fmtSen = '<' + str(numMuestras) + 'h'
        
        semisuma = signal[0][::2]
        semidiferencia = signal[0][1::2]
        
        sen = [None] * (len(semisuma) + len(semidiferencia))
        sen[::2] = [v1 + v2 for v1, v2 in zip(semisuma, semidiferencia)]
        sen[1::2] = [v1 - v2 for v1, v2 in zip(semisuma, semidiferencia)]
        
        fpWave.write(struct.pack(fmtSen, *sen))


# Comprobación de las funciones
signal, sampleRate = leeWave('wav/komm.wav')
writeWave("copia.wav", signal, sampleRate)
estereo2mono("wav/komm.wav", "izquierdo.wav", 0)
estereo2mono("wav/komm.wav", "derecho.wav", 1)
estereo2mono("wav/komm.wav", "semisuma.wav", 2)
estereo2mono("wav/komm.wav", "semidiferencia.wav", 3)
mono2estereo("izquierdo.wav", "derecho.wav", "Conjunto.wav")
codEstereo('wav/komm.wav', 'Codificado.wav')
decEstereo('Codificado.wav', 'DesCodificado.wav') 
```


#### Subida del resultado al repositorio GitHub y *pull-request*

La entrega se formalizará mediante *pull request* al repositorio de la tarea.

El fichero `README.md` deberá respetar las reglas de los ficheros Markdown y visualizarse correctamente en
el repositorio, incluyendo el realce sintáctico del código fuente insertado.
