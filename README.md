# D_GlovePair
Repo for the GlovePair Plugin

Story:
* das erste Mal
- Anleitung für COLLECTOR öffnen #MOM-Plattform
- COLLECTOR bauen
  - upload Firmware
- System installiert MOM-Basis/Plugin === Guh/Plugin auf der Basis//<-- Hat Guh da schon ein Management-Tool?
- System erzeugt COLLECTOR/DeviceAgent === Guh/Device für MOM-Glove
  - Guh/Plugin/deviceClass hängt vom COLLECTOR--TYPE_OF--C_TYPE ab
    - Guh/deviceClass
      .id = UUID //<-- wird von MOM-Plattform erzeugt
      .name = "MOM-Glove" //<-- wird von Admin auf MOM-Plattform festgelegt
  //Allgemein
  - Guh/Device.id = COLLECTOR.id //<-- wird von MOM-Plattform erzeugt
  - Guh/Device.name = COLLECTOR.name //<-- wird von MOM-Plattform erzeugt
  //paramTypes
  - collectionID = COLLECTION.id
  - bluetoothMac = COLLECTOR.macAdress //<-- wird von USER über MOM-Plattform festgelegt
  - bluetoothPassword = COLLECTOR.password //gibt es da bessere Pairing-Methoden? <-- wird von USER über MOM-Plattform festgelegt
  //stateTypes
  - conected
  - handIsInGlove
  - collectorTagDetected
  - lastTagId
  - userInputButtonPressed
  - error (optional)
  //actionTypes
  //methods
  - create: User
- Pairing zwischen COLLECTOR/DeviceAgent und COLLECTOR/Device ausführen
  - Bluetooth-Pairing via Code-Eingabe //<-- geht das überhaupt oder machen wir das wie bei MOM-Trainer direkt über Ubuntu
- System erzeugt COLLECTOR/DeviceAgent für MOM-GlovePair
  - Guh/Plugin/deviceClass hängt vom COLLECTOR--TYPE_OF--C_TYPE ab
    - Guh/deviceClass
      .id = UUID //<-- wird von MOM-Plattform erzeugt
      .name = "MOM-GlovePair" //<-- wird von Admin auf MOM-Plattform festgelegt
  //Allgemein
  - Guh/Device.id = COLLECTOR.id //<-- wird von MOM-Plattform erzeugt
  - Guh/Device.name =  COLLECTOR.name //<-- wird von MOM-Plattform erzeugt
  //paramTypes
  - collectionID = COLLECTION.id (optional) <-- im Standardfall sammelt dieses Device keine Daten in der MongoDB
  //stateTypes
  - recording
  - error (optional)
  //actionTypes
  - startRecord
  - stopRecord
  //methods
  - create: User
- Pairing zwischen MOM-Glove und MOM-GlovePair. Die Zuordnung zwischen COLLECTOR und Hand des HOSTs wird nicht im Plugin, sondern auf der MOM-Plattform via COLLECTION.id vorgenommen
- COLLECTOR anlegen <-- COLLECTOR (genauer das "MOM-GlovePair" Plugin) kann erst aufnehmen (bzw. einen RECORD erstellen), wenn mind. ein COLLECTOR benutzt (COLLECTOR.handIsInGlove=true) wird
* WORKER startet Aufnahme
  - via Knopfdruck (userInputButton=true) oder via Interface (= Guh/JSON-RPC-Interface)
  - MOM-GlovePair Plugin schreibt die DATA je Handschuhe, sofern vorhanden, in je einen eigenen RECORD je Hanschuh
    - erstellen und befüllen eines RECORD (= MongoDB/Collection, name = "%COLLECTION.id%-%DATETIME%") mit DATA
* WORKER beendet Aufnahme
  - via Knopfdruck (userInputButton=true), via Interface oder wenn beide COLLECTOR.handIsInGlove=false
* WORKER startet Aufnahme _ERNEUT_
  - neuer RECORD je COLLECTION wird erzeugt und DATA geschrieben
* COLLECTOR wird von einem anderen WORKER benutzt
  - die Devices von MOM-Glove (left|right) werden gelöscht, müssen mit einer anderen COLLECTION.id neu erzeugt und dem #MOM-GlovePair neu zugewiesen  werden.
* COLLECTOR ändert seine Eigenschaften
  - siehe "COLLECTOR wird von einem anderen WORKER benutzt"


TODO:

Anleitung:
- immer wenn ein Guh/Device aus einem Guh/Plugin instanziert wird,
wird eine (neue, sofern noch nicht vorhanden) COLLECTION erstellt
  - ist eine COLLECTION mit angegebener ID bereits vorhanden,
    so werden neue DATA (entsprechend der SIGNAL.id) nach den alten
    DATA hinzugefügt
  - ist eine COLLECTION mit angegebener ID nicht vorhanden,
    so wird eine neue COLLECTION erstellt und die DATA danach
    abgespeichert
- löscht man ein Guh/Device (in Guh), so bleiben die verbundenen
COLLECTIONs erhalten
