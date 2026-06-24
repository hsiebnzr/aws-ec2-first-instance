# aws-ec2-first-instance
EC2 Windows-Instanz, erstes AWS-Projekt

# EC2 Windows-Instanz mit IIS-Webserver

Mein erstes AWS-Projekt. Ziel war es, eine EC2-Instanz selbst zu erstellen, sich per RDP zu verbinden, einen Webserver zu installieren und am Ende eine eigene Webseite öffentlich aus dem Internet erreichbar zu machen.

## Ziel

- Eine EC2-Instanz (Windows Server) erstellen und sich per RDP verbinden
- Auf der Instanz einen Webserver (IIS) installieren
- Eine eigene, einfache HTML-Seite veröffentlichen
- Die Seite von einem anderen Rechner aus über die öffentliche IP erreichbar machen
- Die Instanz danach kontrolliert wieder schließen, um Kosten zu vermeiden

## Technische Details

| Eigenschaft | Wert |
|---|---|
| Instanztyp | t3.micro (Free Tier) |
| Betriebssystem | Windows Server |
| Region | us-east-1 (N. Virginia) |
| Webserver | IIS (Internet Information Services) |
| Verbindung | RDP über die AWS-Konsole |

## Vorgehen

### 1. Instanz erstellen und verbinden

Über die EC2-Konsole eine Windows-Instanz erstellt und über den browserbasierten RDP-Client verbunden.

[Verbindung aufbauen] 
<img width="1915" height="991" alt="Image" src="https://github.com/user-attachments/assets/79684d2a-e8f4-41af-a465-a55416d1f9c2" />

[Erfolgreich verbunden] 
<img width="1919" height="1045" alt="Image" src="https://github.com/user-attachments/assets/61bff7be-9fca-404f-a9f4-1b351ac14d4f" />

### 2. IIS-Webserver installieren

Auf der Instanz PowerShell als Administrator geöffnet und den Webserver installiert:

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

[IIS Installation erfolgreich] 
<img width="1918" height="985" alt="Image" src="https://github.com/user-attachments/assets/71033085-34ae-488c-990f-16b2bb9e4ca1" />

Die Standard-Startseite von IIS war danach sofort über `localhost` erreichbar:

[IIS Standardseite]
<img width="1913" height="1025" alt="Image" src="https://github.com/user-attachments/assets/f6c75049-6986-4382-845f-467cd2d1e7fe" />

### 3. Eigene Startseite erstellen

Damit es eine eigene Seite und nicht nur die Microsoft-Standardseite ist, wurde eine eigene `index.html` direkt per PowerShell erzeugt:

```powershell
Set-Content -Path "C:\inetpub\wwwroot\index.html" -Value "<html><body style='font-family:Arial;text-align:center;margin-top:100px'><h1>Hallo, das ist mein erstes AWS-Projekt</h1><p>EC2-Instanz mit IIS-Webserver, erstellt von Hsieb</p></body></html>"
```

[Eigene Seite per Befehl erstellt] 
<img width="1890" height="1029" alt="Image" src="https://github.com/user-attachments/assets/30265e5e-be8a-4e84-b118-8cbc9758fb37" />

### 4. Von außen erreichbar machen

In der Security Group der Instanz eine Inbound-Regel für HTTP (Port 80) mit Quelle `0.0.0.0/0` hinzugefügt. Danach war die Seite nicht nur lokal auf der Instanz, sondern auch von einem ganz normalen Browser auf dem eigenen Rechner über die öffentliche IP erreichbar.

![Öffentlicher Zugriff von außen] 
<img width="1919" height="1079" alt="Image" src="https://github.com/user-attachments/assets/3eede6de-50f4-4259-9454-a9f941c59845" />

### 5. Aufräumen

Nach dem erfolgreichen Test wurde die Instanz zunächst gestoppt und der Status überprüft:

[EC2 Dashboard, Instanz gestoppt]
<img width="1919" height="1079" alt="Image" src="https://github.com/user-attachments/assets/733c9193-5c07-4b93-b262-ab7c616efbe4" />

Da die Instanz nicht mehr benötigt wurde, wurde sie anschließend vollständig terminiert (beendet), damit keine weiteren Kosten entstehen können.

## Ergebnis

Eine EC2-Instanz wurde von Grund auf erstellt, konfiguriert und mit einem funktionierenden Webserver ausgestattet. Die eigene Seite war am Ende sowohl lokal (`localhost`) als auch öffentlich über die Internet-IP der Instanz erreichbar.

## Lessons Learned

- Eine gestoppte EC2-Instanz verursacht keine Rechenkosten mehr, die angehängte EBS-Festplatte aber weiterhin geringe Kosten, solange sie existiert. Ein vollständiges Terminieren entfernt auch diese.
- Sicherheitsgruppen steuern, welcher Netzwerkverkehr eine Instanz erreichen darf. Ohne eine passende Inbound-Regel für Port 80 bleibt ein Webserver von außen unerreichbar, selbst wenn er lokal funktioniert.
- `localhost` bezieht sich immer auf den Rechner, auf dem man sich gerade befindet. Um eine Seite von außen zu testen, braucht man die öffentliche IP der Instanz, nicht `localhost`.
- Ein offener Port 80 mit Quelle `0.0.0.0/0` ist für einen kurzen Test unkritisch, sollte aber nach dem Test wieder geschlossen oder die Instanz gestoppt werden, um die Angriffsfläche gering zu halten.

## Hinweis zu Screenshots

Sensible Informationen wie Passwörter, AWS Account-IDs und öffentliche IP-Adressen wurden auf den Screenshots vor der Veröffentlichung unkenntlich gemacht.
