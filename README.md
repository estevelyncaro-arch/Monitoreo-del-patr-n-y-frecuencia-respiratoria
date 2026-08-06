# Monitoreo del patron y frecuencia respiratoria
- María Angel Benavides Silva - 5600852 - est.mariaa.benavides@unimilitar.edu.co
- Evelyn Marcela Caro Rodríguez - 5600848 - est.evelyn.caro@unimilitar.edu.co

## Parte A

La ventilación pulmonar es el proceso mediante el cual se transporta gas desde el entorno hasta los alveolos, su propósito es transportar el oxígeno a los alveolos para permitir el intergambio gaseoso y la expulsión de dióxido de carbono. El flujo de gases puede ser activo o pasivo dependiendo de si es espontáneo, es decir por actividad muscular o mecánico, es decir por un mecanismo externo [3].

Una vez el gas llega al alvéolo, el transporte hacia la sangre depende de la difusión pasiva, regida por la Ley de Fick que establece que la cantidad de gas que atraviesa la membrana es proporcional a la superficie de la misma, a una constante de difusión y a la diferencia de presión parcial, e inversamente proporcional al espesor de la membrana . Clínicamente, se mide la capacidad de difusión del monóxido de carbono (DLCO) para evaluar la superficie vascular disponible para el intercambio [4].

Adicionalmente hay algunas mediciones descritas por los volumenes y capacidades pulmonares siendo los siguientes: 

 - Volúmenes Estáticos: Aquellos que no se movilizan habitualmente, como el Volumen Residual (VR), que es el aire que queda tras una espiración forzada [4]. Su determinación requiere métodos físicos como la pletismografía corporal, basada en la Ley de Boyle (P1⋅V1=P2⋅V2), o el método de dilución de gases inertes como el helio [4].
 - Volúmenes Dinámicos: Involucran el factor tiempo y se miden mediante espirometría, destacando el VEF1 (Volumen Espiratorio Forzado en el primer segundo) [1].
 - Capacidades: Son combinaciones de dos o más volúmenes [1]. Por ejemplo, la Capacidad Pulmonar Total (CPT) es la suma de la capacidad vital y el volumen residual [3].

Dentro este proceso respiratorio se encuentran diferentes variables físicas y mecánicas, las cuales se explican a continuación:

- ### Variables físicas:
  - Presión: Es la relación entre la fuerza y la superficie (P=F/S) y se mide usualmente en cmH2O
  - Flujo: Es el movimiento de gas por unidad de tiempo, determinado por la relación entre la presión y la resistencia (F=P/R), expresado habitualmente en L/min o L/seg
  - Volumen:Representa la cantidad de gas que se mueve o que contienen los pulmones, medida en mililitros (ml) o litros (L) 
  - Tiempo: Es el factor que, al interactuar con el flujo, determina el volumen final alcanzado en un ciclo  [3].

- ### Variables mecánicas
  - Elasticidad: Es la capacidad de los tejidos pulmonares para volver a su posición inicial tras una deformación; en fisiología, se cuantifica como el cambio de presión respecto al cambio de volumen [3].
  - Viscosidad: Depende de la fricción interna entre el tejido pulmonar y el gas que circula por las vías aéreas [3].
  - Tensión Superficial: Es producida por fuerzas cohesivas en la capa de superficie alveolar, las cuales dependen de la curvatura y composición del fluido [3].
  - Histéresis: Fenómeno donde el efecto de una fuerza sobre el pulmón persiste más allá de la duración de la fuerza misma [3].
  - Distensibilidad o compliancia: Es la medida de la facilidad con la que el pulmón se expande [4]; matemáticamente es el inverso de la elastancia (D=ΔV/ΔP) [2].
  - Resistencia : Es el impedimento al flujo de aire causado por el roce con las paredes de las vías aéreas y el tejido funconal de los pulmones [4].

La interacción de estas variables se resume en la ecuación de movimiento del sistema respiratorio, la cual establece que la presión total necesaria para insuflar el pulmón debe ser capaz de vencer la presión resistiva y la presión elástica.
Presión total = (Flujo × Resistencia) + (Volumen × Elastancia). En ventilación asistida, esta presión total es la suma de la presión generada por los músculos del paciente y la generada por el ventilador [2].

## 

De acuerdo a la literatura mencionadaa anteriormente se decidió implementar un sensor resistivo FSR-402 junto con una banda elástica con el fin de adquirir la señal respiratoria. El sensor es ubicado en el torax del paciente, al respirar el pecho se expande y aplica una presión al sensor, esta presión hace que disminuya su resistencia aumentando de esta manera el voltaje, por otro lado al exalar esta presión dismminuye aumentando la resistencia del sensor lo que provoca que disminuya el voltaje. 

Para realizar la captura de la señal se realiza un circuito de adquisición y digitalización, lo cual se realiza construyendo un divisor de voltaje con una resistencia de 100 KΩ conectada a un pin del sensor resistivo (FSR-402) al cual vamos a llamar "pin 1"  y así mismo conectada a tierra (GND), el otro pin del sensor "pin 2" se conecta a 5V suministrados por un DAQ, para la lectura de la señal se conecta el "pin 1" a un pin de salida del DAQ como se muestra en las siguientes imágenes.

<img width="1280" height="527" alt="image" src="https://github.com/user-attachments/assets/1aba1353-9dd9-46f2-b2f6-bd8aa27e09fc" />


<img width="591" height="793" alt="image" src="https://github.com/user-attachments/assets/9cf36eaf-0c93-4d85-a397-f28fe582ea65" />


Una vez se verifica el funcionamiento del circuito realizado se procede a realizar la captura de datos.

## Parte B 

Para la captura de la señal respiratoria se desarrolla un código en MATLAB, el cual realiza la adquisición de los datos obtenidos por el sensor, los guarda y los grafica, mostando la señal correspondiente al patrón respiratorio, se filtra la señal para eliminar ruido y se ubican los picos de cada respiración. A continuación se explica detalladamente el código implementado.

```matlab
%%=========================================================
% LABORATORIO DE INSTRUMENTACIÓN BIOMÉDICA
%%=========================================================

clear
clc
close all

%% CONFIGURACIÓN

Fs = 100;                  % Frecuencia de muestreo (Hz)
Tiempo = 30;               % Tiempo de adquisición (s)

%% CREAR DAQ

d = daq("ni");

addinput(d,"Dev1","ai0","Voltage");

d.Rate = Fs;

disp("Iniciando adquisición durante 30 segundos...")

%% ADQUISICIÓN

datos = read(d,seconds(Tiempo));

disp("Adquisición terminada.")

%% VER NOMBRE DE LAS VARIABLES (opcional)

disp(datos.Properties.VariableNames)

%% EXTRAER DATOS

tiempo = seconds(datos.Time);

senal = datos{:,1};      % Primera columna sin importar el nombre

%% GUARDAR DATOS

save("Respiracion30s.mat","senal","tiempo","Fs")

%% GRAFICAR

figure

plot(tiempo,senal,'b','LineWidth',1.5)

grid on

xlabel('Tiempo (s)')
ylabel('Voltaje (V)')
title('Señal respiratoria (30 segundos)')

%% ELIMINAR OFFSET

senal = senal - mean(senal);

%% FILTRO PASA BAJAS

Fc = 2;

Orden = 4;

[b,a] = butter(Orden,Fc/(Fs/2),"low");

senal_filtrada = filtfilt(b,a,senal);

%% GRAFICAR FILTRADA

figure

plot(tiempo,senal_filtrada,'r','LineWidth',2)

grid on

xlabel('Tiempo (s)')
ylabel('Voltaje (V)')
title('Señal respiratoria filtrada')

%% FFT

L = length(senal_filtrada);

Y = fft(senal_filtrada);

P2 = abs(Y/L);

P1 = P2(1:floor(L/2)+1);

P1(2:end-1)=2*P1(2:end-1);

f = Fs*(0:floor(L/2))/L;

figure

plot(f,P1,'k','LineWidth',2)

grid on

xlabel('Frecuencia (Hz)')
ylabel('Magnitud')

title('Espectro de frecuencia')

xlim([0 5])

%% FRECUENCIA DOMINANTE

Paux = P1;

Paux(1)=0;

[~,indice] = max(Paux);

FrecuenciaDominante = f(indice);

RPM_FFT = FrecuenciaDominante*60;

%% DETECCIÓN DE PICOS

[picos,locs] = findpeaks( ...
    senal_filtrada,...
    'MinPeakDistance',round(Fs*1.5),...
    'MinPeakProminence',0.02);

Respiraciones = length(picos);

RPM_Picos = Respiraciones*60/Tiempo;

%% RESPIRACIONES DETECTADAS

figure

plot(tiempo,senal_filtrada,'b')

hold on

plot(tiempo(locs),picos,'ro','MarkerFaceColor','r')

grid on

xlabel('Tiempo (s)')
ylabel('Voltaje (V)')
title('Respiraciones detectadas')

%% RESULTADOS

fprintf('\n');

fprintf('Frecuencia dominante: %.3f Hz\n',FrecuenciaDominante);

fprintf('RPM por FFT: %.2f\n',RPM_FFT);

fprintf('Respiraciones detectadas: %d\n',Respiraciones);

fprintf('RPM por picos: %.2f\n',RPM_Picos);

%% GUARDAR RESULTADOS

Resultados.Tiempo = tiempo;
Resultados.Senal = senal;
Resultados.SenalFiltrada = senal_filtrada;
Resultados.RPM_FFT = RPM_FFT;
Resultados.RPM_Picos = RPM_Picos;

save("ResultadosRespiracion.mat","Resultados");

disp("Proceso finalizado correctamente.")
```

## Referencias 


[1] Universidad Abierta Interamericana, Mecánica ventilatoria [En línea]. Disponible en: https://dspaceapi.uai.edu.ar/server/api/core/bitstreams/11584f04-cfc9-422a-a682-486fec1ff9ee/content

[2] Sociedad Catalana de Medicina Intensiva y Crítica (SCARTD), Fisiología respiratoria, 2006. [En línea]. Disponible en: https://scartd.org/arxius/fisioresp06.pdf

[3] Sociedad Catalana de Medicina Intensiva y Crítica (SCARTD), Fisiología respiratoria, 2006. [En línea]. Disponible en: https://scartd.org/arxius/fisioresp06.pdf

[4] Facultad de Medicina, Universidad Nacional Autónoma de México (UNAM), Mecánica de la ventilación pulmonar y espirometría. [En línea]. Disponible en: https://fisiologia.facmed.unam.mx/index.php/mecanica-de-la-ventilacion-pulmonar-espirometria/
