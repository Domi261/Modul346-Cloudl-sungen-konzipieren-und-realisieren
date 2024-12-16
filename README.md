### Projektstruktur
Hier in der Projektstruktur sehen wir alle unsere Files, welche wir für das Projekt benötigen.
Im nächsten Schritt werden alle notwendigen Tool für die Nutzung installiert.
```
Modul346-Cloudloesungen-konzipieren-und-realisieren/
├── lambda_function.py   # Hauptskript für die Lambda-Funktion
├── requirements.txt    # Python-Abhängigkeiten
├── example.csv         # Beispiel-CSV-Datei
├── event.json          # Simuliertes Event für lokale Tests
└── README.md          # Dokumentation
```

---

### Schritte zur Installation der benötigten Tools (Linux)

---

#### 1. **Repository klonen**
Bevor du Abhängigkeiten installieren kannst, klone zuerst das Repository:
```bash
git clone https://github.com/Logoko709/Modul346-Cloudloesungen-konzipieren-und-realisieren.git
cd Modul346-Cloudloesungen-konzipieren-und-realisieren
```
---

#### 2. **AWS CLI installieren**
- **Installation auf Ubuntu/Debian:**
  ```bash
  sudo apt update
  sudo apt install awscli -y
  ```
- **Verifizierung:**  
  Mit folgendem Befehl lässt sich überprüfen, ob die Installation erfolgreich war:
  ```bash
  aws --version
  ```

---

#### 3. **Python 3.x und pip installieren**
- **Installation auf Ubuntu/Debian:**
  ```bash
  sudo apt update
  sudo apt install python3 python3-pip -y
  ```
- **Verifizierung:**  
  Überprüfe die installierte Version von Python und pip:
  ```bash
  python3 --version
  pip3 --version
  ```

---

#### 4. **Abhängigkeiten aus `requirements.txt` installieren**
- **Installiere die Abhängigkeiten:**  
  Nutze pip, um die Pakete aus der `requirements.txt` zu installieren:
  ```bash
  pip3 install -r requirements.txt
  ```
- **Verifizierung der boto3-Installation:**  
  Überprüfe, ob `boto3` korrekt installiert wurde:
  ```bash
  pip3 show boto3
  ```

---

### Alles bereit!  
Die notwendigen Tools und Pakete sind installiert. Du kannst nun mit der Entwicklung oder Ausführung des Projekts fortfahren.
------------------------------------------------------------
4. Erstelle die S3-Buckets
   Die Funktion erstellt automatisch die erforderlichen S3-Buckets, falls sie noch nicht existieren:
   Input-Bucket: inputbucketcsvtojson
   Output-Bucket: outbucketcsvtojson

   Alternativ manuell:
   aws s3 mb s3://inputbucketcsvtojson
   aws s3 mb s3://outbucketcsvtojson

5. Lade eine CSV-Datei hoch
   aws s3 cp example.csv s3://inputbucketcsvtojson/

6. Lokale Simulation der Lambda-Funktion

   Erstelle ein Event-JSON (event.json):
   {
       "Records": [
           {
               "s3": {
                   "bucket": {
                       "name": "inputbucketcsvtojson"
                   },
                   "object": {
                       "key": "example.csv"
                   }
               }
           }
       ]
   }

   Führe die Funktion lokal aus:
   python lambda_function.py

7. Prüfe den Output
   Nach der Verarbeitung erscheint die JSON-Datei im Output-Bucket (outbucketcsvtojson).
   Lade sie herunter, um sie zu prüfen:
   aws s3 cp s3://outbucketcsvtojson/example.json ./output.json

------------------------------------------------------------

Beispiel-CSV
id,name,age,city
1,John Doe,29,Zurich
2,Jane Smith,34,Bern
3,Bob Brown,23,Geneva

Erwartetes JSON-Output:
[
    {
        "id": "1",
        "name": "John Doe",
        "age": "29",
        "city": "Zurich"
    },
    {
        "id": "2",
        "name": "Jane Smith",
        "age": "34",
        "city": "Bern"
    },
    {
        "id": "3",
        "name": "Bob Brown",
        "age": "23",
        "city": "Geneva"
    }
]

------------------------------------------------------------

Deployment in AWS

1. Erstelle die Lambda-Funktion
   Code zippen:
   zip lambda_function.zip lambda_function.py

   Lambda-Funktion erstellen:
   aws lambda create-function \
       --function-name CsvToJsonConverter \
       --runtime python3.x \
       --role <IAM_ROLE_ARN> \
       --handler lambda_function.lambda_handler \
       --zip-file fileb://lambda_function.zip

2. Richte den S3-Trigger ein
   - Öffne den S3-Bucket (inputbucketcsvtojson) in der AWS Management Console.
   - Navigiere zu Properties > Event notifications.
   - Erstelle eine Benachrichtigung:
     Event Type: All object create events
     Lambda Function: CsvToJsonConverter

------------------------------------------------------------

Fehlerbehebung

1. AWS CLI ist nicht installiert
   sudo apt update
   sudo apt install awscli

2. Berechtigungsfehler
   Stelle sicher, dass die IAM-Rolle/Lambda-Funktion Zugriff auf S3 hat:
   Erforderliche Berechtigungen:
   s3:GetObject
   s3:PutObject
   s3:ListBucket

------------------------------------------------------------

Autor
Name: Logoko709
Projektname: Modul346-Cloudlösungen-konzipieren-und-realisieren
