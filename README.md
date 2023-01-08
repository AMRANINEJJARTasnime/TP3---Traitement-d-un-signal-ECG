# TP3 - Traitement d’un signal ECG
## Objectifs 
- Suppression du bruit autour du signal produit par un électrocardiographe. 
- Recherche de la fréquence cardiaque. 
>Commentaires : Il est à remarquer que ce TP traite en principe des signaux continus. 
Or, l'utilisation de Matlab suppose l'échantillonnage du signal. Il faudra donc être 
vigilant par rapport aux différences de traitement entre le temps continu et le temps 
discret.

>Tracé des figures : toutes les figures devront être tracées avec les axes et les 
légendes des axes appropriés.

>Travail demandé : un script Matlab commenté contenant le travail réalisé et des
commentaires sur ce que vous avez compris et pas compris, ou sur ce qui vous a 
semblé intéressant ou pas, bref tout commentaire pertinent sur le TP.

- Introduction 
Un électrocardiogramme (ECG) est une représentation graphique de l’activation 
électrique du cœur à l’aide d’un électrocardiographe. Cette activité est recueillie sur 
un patient allongé, au repos, par des électrodes posées à la surface de la peau. 
L’analyse du signal ECG est utile dans le but de diagnostiquer des anomalies 
cardiaques telles qu’une arythmie, un risque d’infarctus, de maladie cardiaque 
cardiovasculaire ou encore extracardiaque. 

Ci-dessous, voici un schéma représentant une représentation classique d’une courbe 
d’un ECG. Ce schéma se nomme un « Complexe QRS » mettant en évidence le bon 
fonctionnement d’un cycle cardiaque.
<img width="317" alt="intro tp3" src="https://user-images.githubusercontent.com/121026580/211198100-fde05b7a-4ba6-49bb-935d-82f3e6bb624f.png">

L’onde P représente la première étape du cycle où les oreillettes (ou atriums) se 
contractent permettant le passage du sang, à travers les valves auriculoventriculaires, vers les ventricules.
Ensuite, le complexe QRS symbolise à la fois la contraction ventriculaire (permettant 
l’éjection du sang vers les artères) notamment par le pic en R, dans le même temps, 
le relâchement des oreillettes entraîne le remplissage de celles-ci en attente d’un 
nouveau cycle).

L’onde T représente le relâchement des ventricules suite à leur contraction.
L’enchaînement de ces complexes permet par ailleurs de déterminer la fréquence 
cardiaque, c’est-à-dire le nombre de cycle cardiaque par unité de temps. Une 
fréquence cardiaque normale est comprise entre 60 et 100 battements par minute, en 
dessous de cette valeur, le patient est en « bradycardie », au-dessus de cette valeur, 
le patient est en « tachycardie ».

Les signaux ECG sont contaminés avec différentes sources de bruits. Les bruits de 
hautes fréquences sont provoqués par l’activité musculaire extracardiaque et les 
interférences dues aux appareils électriques, et des bruits de basses fréquences 
provoqués par les mouvements du corps liés à la respiration, les changements 
physicochimiques induits par l’électrode posée sur la peau et les micro variations du 
flux sanguin. Le filtrage de ces bruits est une étape très importante pour faire un 
diagnostic réussi.
## Suppression du bruit provoqué par les mouvements 
du corps
1. Sauvegarder le signal ECG sur votre répertoire de travail, puis charger-le dans 
Matlab à l’aide la commande load.
<img width="216" alt="3 1" src="https://user-images.githubusercontent.com/121026580/211209922-427da205-7fc1-41be-8954-54f5232cfc35.png">

2. Ce signal a été échantillonné avec une fréquence de 500Hz. Tracer-le en fonction 
du temps, puis faire un zoom sur une période du signal.

```matlab
x=ecg;
fs=500;
N=length(x)
ts=1/fs
%%tracer ECG en fonction de temps
t=(0:N-1)*ts;
% subplot(2,2,1)
plot(t,x);
%xlim([0.5 1.5])
title("le signal ECG");
```
<img width="326" alt="3 2" src="https://user-images.githubusercontent.com/121026580/211210363-346f5664-2bd9-46b6-9137-506b429c9ba1.png">
<img width="245" alt="xlim" src="https://user-images.githubusercontent.com/121026580/211211192-5d06d8fd-012e-4b23-b5c1-51947f02f374.png">

3. Pour supprimer les bruits à très basse fréquence dues aux mouvements du corps,
on utilisera un filtre idéal passe-haut. Pour ce faire, calculer tout d’abord la TFD du 
signal ECG, régler les fréquences inférieures à 0.5Hz à zéro, puis effectuer une 
TFDI pour restituer le signal filtré.

```matlab
 y = fft(x);
 f = (-N/2:N/2-1)*(fs/N);
% subplot(2,2,2)
% plot(f,fftshift(abs(y)))
title("spectre Amplitude")

%%suppression du bruit des movements de corps

h = ones(size(x));
fh = 0.5;
index_h = ceil(fh*N/fs);
h(1:index_h)=0;
h(N-index_h+1:N)=0;
filtre=h.*y;
ecg1=ifft(filtre,"symmetric");
```


4. Tracer le nouveau signal ecg1, et noter les différences par rapport au signal 
d’origine.

```matlab
plot(t,ecg1);
title("signal filtré")
```

<img width="337" alt="signal filtre" src="https://user-images.githubusercontent.com/121026580/211211466-123950e3-cfc9-43b7-9e60-bcd533ee2231.png">
<img width="250" alt="differ" src="https://user-images.githubusercontent.com/121026580/211212614-278586ea-3516-4d6c-8f57-89e7890a799c.png">

## Suppression des interférences des lignes électriques 50Hz
Souvent, l'ECG est contaminé par un bruit du secteur 50 Hz qui doit être supprimé. 
5. Appliquer un filtre Notch idéal pour supprimer cette composante. Les filtres 
Notch sont utilisés pour rejeter une seule fréquence d'une bande de fréquence 
donnée.

```matlab
Notch = ones(size(x));
fcn = 50;
index_hcn = ceil(fcn*N/fs)+1;
Notch(index_hcn)=0;
Notch(index_hcn+2)=0;

ecg2_freq = Notch.*fft(ecg1);
ecg2 =ifft(ecg2_freq,"symmetric");
% subplot(211)
plot(t,ecg2,'r');            
xlabel('time')
ylabel('amplitude')
% subplot(212)
xlim([0.5 1.5])
title("signal filtré")
```
6. Visualiser le signal ecg2 après filtrage.
<img width="414" alt="filtredsignal11" src="https://user-images.githubusercontent.com/121026580/211217136-f0d7565b-ed33-4133-b803-520cbf2f4cbe.png">
<img width="421" alt="filtredsignal12" src="https://user-images.githubusercontent.com/121026580/211217138-2f69e6de-eb79-436d-954a-67cb8df7347a.png">

## Amélioration du rapport signal sur bruit 
Le signal ECG est également atteint par des parasites en provenance de l’activité 
musculaire extracardiaque du patient. La quantité de bruit est proportionnelle à la 
largeur de bande du signal ECG. Une bande passante élevée donnera plus de bruit 
dans les signaux, et limiter la bande passante peut enlever des détails importants du 
signal. 

7. Chercher un compromis sur la fréquence de coupure, qui permettra de préserver 
la forme du signal ECG et réduire au maximum le bruit. Tester différents choix, puis
tracer et commenter les résultats.
