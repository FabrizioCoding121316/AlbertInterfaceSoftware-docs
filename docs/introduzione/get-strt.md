# **GETTING STARTED**

Questa pagina descrive in dettaglio i prerequisiti, i passaggi di installazione, le variabili dell'ambiente di runtime e gli argomenti della riga di comando necessari per avviare ed eseguire correttamente il software Albert Interface sull'hardware di destinazione (ad esempio un Raspberry Pi 5) o sulle workstation di sviluppo.

## Prequisiti
L'applicazione si basa sulle librerie Python principali, sui demoni di sintesi vocale a livello di sistema e sulle autorizzazioni delle porte seriali per interfacciarsi con il microcontrollore hardware.

### - Pacchetti di sistema
Prima di installare le dipendenze Python, assicurarsi che il sistema operativo sottostante disponga di  sintesi vocale:

 - **Python**: versione `3.14` o superiore
 - **Speach Dispatcher**: demone neccessario per far funzionare la voce del programma
 - **eSpeak NG**: il sitetizzatore della voce

## Requisiti
L'ambiente Python richiedePySide6 l'interfaccia grafica, insieme alle librerie di visione artificiale per il tracciamento dei volti che si possono trovare nel file `requirements.txt`:
````txt
# Albert Assistant - versione Qt
#Obbligatorie (interfaccia, seriale col Pico, rete asincrona, sintesi vocale)
PySide6>=6.6
 
# Facoltative
setuptools==80.10.2
opencv-python==5.0.0.93
numpy==2.5.4
face_recognition_models @ git+https://github.com/ageitgey/face_recognition_models
face_recognition==1.3.0
````

## Variabili d'Ambiente
Il comportamento dell'applicazione può essere personalizzato in fase di esecuzione utilizzando variabili di ambiente. Questi controllano i vincoli di sicurezza, gli endpoint di comunicazione e l'integrazione dell'intelligenza artificiale nel cloud.

| Variabile d'ambiente | Valore default | Descrizione |
| :------------- | :------------: | :------------- |
| OPERATOR_PIN        | 1234        | PIN numerico necessario per sbloccare i pannelli di guida manuale e amministrativi in **`MainWindow`**        |
| PICO_PORT       | /dev/ttyACM0        | Percorso seriale del dispositivo per comunicare con il firmware del microcontrollore **Pico**        |   
| ANTHROPIC_API_KEY | Nessuna | Chiave API per l'integrazione di Anthropic Claude. Se omesso, l'assistente ricorre alla corrispondenza delle FAQ locali (Probabilmente srà cambianta per una opzione meno costosa) |
| ANTHROPIC_MODEL | Nessuna | Specifica il modello di chat bot usato (Correlata ad **`ANTHROPIC_API_KEY`**) |

## Flags
Durante l'esecuzione del punto di ingresso primariomain_ui.py, è possibile fornire diversi flag opzionali della riga di comando per modificare l'ambiente di runtime:

 - **`--sim`** instanzia la classe **Robot** in modalità simulazione bypassando pico.
 - **`--kiosk`** avvio in modalità schermo intero e senza cursore compatibile con **`x11`** e con **`wayland`** tramite **`xwayland`** (non c'è supporto nativo di wayland).
 - **`--no-camera`** cambia la modalità di visione della faccia, con questa opzione attiva, non viene usata la telecamera, se si aggiunge questo flag **`--kiosk`** non funziona.
 - **`--no-salva-volti`** Limita il riconoscimento facciale alla elaborazione in ram senza creare un database ne una cartella con i volti.

## Struttura File

````
AlbertInterfaceSoftware/
    |
    --LICENSE
    |
    --robot.py                  #Ponte verso il Raspberry Pi Pico (USB seriale) con modalità simulazione
    |
    --applog.py                 #Gestisce i log del programma
    |
    --requirements.txt          #File in cui si trovano le dipendenze
    |
    --main_ui.py                #File Python per l'inizializzazione del programma
    |
    --classes/
    |   |
    |   --config.py             #File Python con alcune cotanti necessarie come la directory del data base e i folder di immagini
    |   |
    |   --console_view.py       #File Python contenete la classe che permette la visone del console log  dentro il programma
    |   |
    |   --face_widget.py        #File Python contenete il widget che disegna la faccia dell'interfaccia
    |   |
    |   --face_worker.py        #Fil incaricato di gestire il movimento della faccia attraverso il riconoscimento facciale, se il flag --no-camara è attivo, non viene usato
    |   |
    |   --main_window.py        #Incaricato di costuire tutta la finestra
    |   |
    |   --style.py              #File incaricato di applicare lo style.qsss
    |   |
    |   --utils.py              #Serve per creare dei Labels
    |   |
    |   --_pycache__/           #Contenente i file bytecode delle classi
    |
    --pico/
    |   |
    |   --main.py               #Gestisce la parte hardware del robot (deve essere modificato per l'uso)
    |
    --styles/
        |
        --main_style.qss        #File QSS dove si trova lo stylesheet dell'interfaccia
        |
        --themecolor.conf       #File .conf dove si trovano i colori dell'interfaccia, a differenze del file conf.python, questo file modifica i colori usati nell'interfaccia

````

