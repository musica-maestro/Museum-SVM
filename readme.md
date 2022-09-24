# Obiettivo

L'obiettivo di questo progetto è realizzare un modello che a partire dai dati relativi al volto di un osservatore di un'opera d'arte possa predire i suoi livelli di arousal, valence, likability, rewatch e familiarity.

In particolare, l'arousal e la valence indicano rispettivamente lo stato di eccitazione che si manifesta in risposta ad uno stimolo e quanto questo sia positivo o negativo. La likability indica quanto uno stimolo sia piaciuto. Con la rewatch si classifica il desiderio di rivedere un certo stimolo. La familiarity indica se input era già noto allo spettatore.

# Dati

Il dataset è stato ottenuto sfruttando un sito realizzato ad hoc ed una piattaforma di crowdsourcing, Prolific, per la ricerca dei partecipanti.

Durante la sperimentazione si è mostrato ad ogni utente un totale di 12 opere d'arte. Le reazioni dell'utente venivano registrate attraverso la sua webcam e dopo ogni stimolo veniva richiesto di indicare esplicitamente i feedback relativi ai parametri appena descitti. Di conseguenza, per ogni partecipante sono stati ottenuti 12 video e 5 feedback espliciti per video più i dati relativi ad un questionario finale.

Alla fine della spetimentazione si sono ottenuti i dati relativi a 111 partecipanti.

# Modello SVM

In questa repository sono contenuti i codici, ed i risultati, relativi a dei modelli basati su SVM. 

## Preparazione dei Dati

I video dei partecipanti sono stati analizzati attraverso un software open source, OpenFace, che ci ha permesso di estrarre dei dati numerici.
Da ogni video abbiamo ottenuto per ogni frame i livelli delle Action Units, che descrivono tutti i movimenti facciali visivamente distinguibili. 

A partire dall'output di OpenFace sono state create più tabelle a seconda della tipologia di segnale: continuo, quantizzato, binario.

Per ogni action unit sono poi state estratte le seguenti feature: media, mediana, varianza, curtosi, deviazione standard, massimo. 

A queste si aggiungono, per i segnali quantizzati e binari, anche le feature discrete e dinamiche: rapporto di attivazione, lunghezza di attivazione, livello di attivazione, rapporto di cambiamento, rapporto di cambiamento lento, rapporto di cambiamento veloce.

Nella cartella tables sono contenuti quindi quattro file relativi alle diverse tipologie di segnale:
- R -> Segnale continuo
- RC -> Segnale continuo pulito azzerando il rumore, dove per rumore si intendono i valori di Open Face minori di 1
- Q -> Segnale quantizzato
- C -> Segnale Binario

Ogni riga corrisponde ai valori ottenuti per un video. Le ultime 5 colonne sono relative ai valori di aorusal, valence, rewatch, likability, familiarity.

## Divisione Training e Test, Baseline

Train e test sono stati costruiti utilizzando l'opzione di stratify, offerta da sklearn, in modo che ogni insieme contenesse all'incirca la stessa percentuale di campioni di ogni classe target dell'insieme completo.

Per ogni label è stata calcolata una baseline, cioè l'accuracy minima che il modello dovesse avere conoscendo la distribuzione dei dati.

Visto che in questo caso il modello è una SVM le feature sono state scalate attraverso lo standard scaler di sklearn.

## Training

Ogni cartella, nominata in base al segnale preso in analisi, contiene 5 notebook, ognuno relativo ad una label.

Il training è stato effettuato utilizzando una grid search con cross validation su 5 fold.

## Risultati

L'ultima cella di ogni notebook contiene una matrice di confusione e un report che riporta i valori di precision, recall ed F1 score.

Per ogni tipologia di segnale e per ogni label sono stati ottenuti dei risultati molto vicini alla baseline, di poco superiori o inferiori.

Ad esempio si riporta qui sotto la matrice di confusione relativa alla rewatch del segnale continuo.
```
[[ 4 48  9]
 [10 67 19]
 [ 7 63 16]]
```
Spicca che le predizioni sono abbastanza sbilanciate sulla classe 2. Questo fenomeno è ancora più marcato per quanto riguarda altri test. Di seguito la matrice relativa alla likability del segnale discreto.
```
[[  0  62   0]
 [  0 102   0]
 [  0  79   0]]
 ```
 Di conseguenza possiamo stabilire che questo tipo di modello non riesca ad apprendere nulla utilizzando i dati in questo modo.