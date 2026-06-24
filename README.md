# Mein erstes AWS-Projekt: Eine eigene Webseite auf einer EC2-Instanz

Ich befinde mich gerade im Wechsel von einem Informatikstudium in eine duale IT-Ausbildung. Theorie allein hat mir nie gereicht, ich wollte schon immer lieber direkt ausprobieren, wie etwas funktioniert. AWS stand seit längerem auf meiner Liste, also habe ich mir vorgenommen, ein kleines, aber vollständiges Projekt durchzuziehen: eine eigene EC2-Instanz erstellen, einen Webserver installieren und am Ende eine eigene Seite wirklich aus dem Internet erreichbar machen. Hier ist, wie es gelaufen ist.

## Schritt 1: Die Instanz erstellen und sich verbinden

Der erste Teil war reine Handarbeit in der AWS-Konsole: eine Windows-Instanz (t3.micro, Free Tier) anlegen und sich darüber per RDP-Client direkt im Browser verbinden, ganz ohne zusätzliche Software auf dem eigenen Rechner.

[Verbindungsaufbau]
<img width="1915" height="991" alt="Image" src="https://github.com/user-attachments/assets/66dfb950-503f-4c3f-8f9a-7fdcaf4e5293" />

Kurz darauf stand die Verbindung, und ich konnte die Systeminformationen direkt auf dem Desktop der Instanz sehen, Hostname, Instance-ID, Instanzgröße und Availability Zone.

[Erfolgreich verbunden mit Systeminfo]
<img width="1919" height="1045" alt="Image" src="https://github.com/user-attachments/assets/d0a34f9f-08f7-4368-a253-976f40d4f159" />

## Schritt 2: Einen Webserver installieren

Beim nächsten Anlauf ging es ans Eigentliche. Ich habe mich wieder verbunden, PowerShell als Administrator geöffnet und mit einem einzigen Befehl den Windows-eigenen Webserver IIS installiert:

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

Die Ausgabe zeigte "Success: True", die Installation war also durchgelaufen, ganz ohne Neustart.

[IIS-Installation erfolgreich] 
<img width="1913" height="1025" alt="Image" src="https://github.com/user-attachments/assets/45b4e00c-3681-42a0-ab61-35bc8700c3a2" />

Ein Aufruf von `localhost` direkt auf der Instanz bestätigte, dass der Server lief. Windows Server bringt eine hübsche mehrsprachige Standardseite mit, die sofort erschien.

[Standard-IIS-Seite]
<img width="1890" height="1029" alt="Image" src="https://github.com/user-attachments/assets/f265f6d6-4b55-41dc-8473-779748a9689c" />

## Schritt 3: Eine eigene Seite veröffentlichen

Die Standardseite war ein guter Beweis, dass alles läuft, aber ich wollte natürlich etwas Eigenes zeigen. Mit einem weiteren PowerShell-Befehl habe ich eine eigene `index.html` direkt in den Webserver-Ordner geschrieben:

```powershell
Set-Content -Path "C:\inetpub\wwwroot\index.html" -Value "<html><body style='font-family:Arial;text-align:center;margin-top:100px'><h1>Hallo, das ist mein erstes AWS-Projekt</h1><p>EC2-Instanz mit IIS-Webserver, erstellt von Hsieb</p></body></html>"
```

[Eigene Seite per Befehl erstellt]
<img width="1919" height="1079" alt="Image" src="https://github.com/user-attachments/assets/24dff75e-15d4-49eb-a8d1-3cd36a17e88b" />

## Schritt 4: Der Moment der Wahrheit, Zugriff von außen

Lokal lief alles, aber der eigentliche Test war: Kann ich diese Seite auch von meinem eigenen Rechner aus aufrufen, über das echte Internet? Dafür musste ich in der Security Group der Instanz eine Inbound-Regel für HTTP (Port 80) öffnen.

Und tatsächlich, beim Aufruf der öffentlichen IP-Adresse in meinem eigenen Browser erschien genau dieselbe Seite, die vorher nur lokal auf der Instanz zu sehen war.

[Öffentlicher Zugriff von außen] 
<img width="1919" height="1079" alt="Image" src="https://github.com/user-attachments/assets/077bf44d-ee88-4681-957b-ade22701cae8" />

## Was ich dabei gelernt habe

- Eine gestoppte Instanz kostet keine Rechenzeit mehr, die angehängte Festplatte aber schon, ein vollständiges Terminieren beendet wirklich alles
- Ohne eine passende Inbound-Regel in der Security Group bleibt ein Webserver von außen unsichtbar, egal wie gut er lokal funktioniert
- `localhost` bedeutet immer "dieser Rechner hier", für einen Test von außen braucht man stattdessen die öffentliche IP der Instanz
- Ein offener Port 80 für die ganze Welt ist für einen kurzen Test in Ordnung, sollte danach aber wieder geschlossen werden
