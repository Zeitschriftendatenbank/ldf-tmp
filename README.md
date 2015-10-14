# ldf-server
http://10.100.20.57:5000/zdb

Alle triple, die URI als Subject:

http://10.100.20.57:5000/zdb?subject=http%3A%2F%2Fld.zdb-services.de%2Fresource%2F5-x

Alle triple, die URI als Object:

http://10.100.20.57:5000/zdb?object=http%3A%2F%2Fid.loc.gov%2Fvocabulary%2Fiso639-2%2Fger

- keine SPARQL-Logik
- alle Triple, die die Bedingung erfüllen
- 0/1 Triple wenn SPO besetzt, alle Triple wenn keins besetzt


# ldf-browser/client

- ganze SPARQL-Logik: Parsen, Anfragen, Filtern, Sortieren etc.

http://b-app-zdb.sbb.spk-berlin.de/dev1/ldf/

Languages LIMIT 10:

http://b-app-zdb.sbb.spk-berlin.de/dev1/ldf/#datasources=http%3A%2F%2F10.100.20.57%3A5000%2Fzdb&query=SELECT%20DISTINCT%20%3Flang%20WHERE%20{%0A%20%20%3Fs%20dct%3Alanguage%20%3Flang%20.%0A}%0ALIMIT%2010

- in Tabellenform oder Graph

## Federated / verteilte Suche

Neben der Verfügbarkeit ist der größte Vorteil die verteilte Suche, da Linked Data ermöglicht, dass sich verteilte Ressourcen ergänzen (manchmal auch widersprechen).

Verteilte Suchen können diese Informationen zusammenführen.

ZDB Beispiel: http://ld.zdb-services.de/data/1000402-6.rdf

- ZDB enthält GND-URIs aber keine Labels

Ohne GND:

http://b-app-zdb.sbb.spk-berlin.de/dev1/ldf/#datasources=http%3A%2F%2F10.100.20.57%3A5000%2Fzdb&query=SELECT%20DISTINCT%20*%20WHERE%20{%0A%20%20%3Fs%20dct%3Acontributor%20%3Fgnd_id%20.%0A}%0ALIMIT%20100

Mit GND:

http://b-app-zdb.sbb.spk-berlin.de/dev1/ldf/#datasources=http%3A%2F%2F10.100.20.57%3A5000%2Fzdb;http%3A%2F%2F10.100.20.57%3A5000%2Fgnd&query=SELECT%20DISTINCT%20*%20WHERE%20{%0A%20%20%3Fs%20dct%3Acontributor%20%3Fgnd_id%20.%0A%20%20%3Fgnd_id%20gndo%3ApreferredNameForTheCorporateBody%20%3Fname%20.%0A}%0ALIMIT%20100

Problem unbekannte Properties:

gndo:preferredNameForTheCorporateBody in der GND-Ontology Property nur für Körperschaften. Was ist mit Personen oder Gebietskörperschaften? Auch diese können Contributor (dct:contributer) in der ZDB sein.

GND Ontolgy Property preferredNameForTheCorporateBody

http://d-nb.info/standards/elementset/gnd#preferredNameForTheCorporateBody

Lösung: Benutze die GND Ontology als datasource:

http://b-app-zdb.sbb.spk-berlin.de/dev1/ldf/#datasources=http%3A%2F%2F10.100.20.57%3A5000%2Fzdb;http%3A%2F%2F10.100.20.57%3A5000%2Fgnd;http%3A%2F%2F10.100.20.57%3A5000%2Fgndont&query=SELECT%20DISTINCT%20*%20WHERE%20{%0A%20%20%3Fs%20dct%3Acontributor%20%3Fgnd_id%20.%0A%20%20OPTIONAL%20{%0A%20%20%20%20%3Fprop%20rdfs%3AsubPropertyOf%20gndo%3ApreferredName%20.%20%23%20Alle%20props%20die%20Subproperties%20sind%0A%20%20%20%20%3Fgnd_id%20gndo%3ApreferredNameForTheCorporateBody%20%3Fname%20.%20%23%20wird%20hier%20eingef%C3%BCgt%0A%20%20%20%20%3Fgnd_id%20rdf%3Atype%20%3Ftype%20.%20%23%20Klasse%20der%20Ressource%0A%20%20}%0A}%0ALIMIT%20100

- Da alle SPARQl-Logik dem Client überlassen wird, sind komplexe Abfragen sehr langsam.
- Der LDF-Client ist also keine Suchmaschine sondern eher ein Data-Extraction-Tool



