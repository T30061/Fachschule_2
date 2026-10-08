
# 🛠️ Windows PowerShell Network Cheat Sheet

Dieses Spickzettel enthält die wichtigsten PowerShell-Befehle für die Netzwerkverwaltung unter Windows. 

> [!WARNING]
> **Administratorrechte erforderlich**
> Befehle, die Systemeinstellungen verändern (z. B. IP-Adressen setzen), müssen in einer PowerShell-Konsole ausgeführt werden, die als **Administrator** geöffnet wurde.

---

### 🔍 Netzwerk & Adapter analysieren

| Befehl | Was macht dieser Command? | Wichtige Parameter & Erklärung |
| :--- | :--- | :--- |
| `Get-NetAdapter` | Listet alle physischen und virtuellen Netzwerkkarte auf. Zeigt Status, MAC-Adresse und Verbindungsgeschwindigkeit. | *Keine Parameter nötig.* Zeigt den `-InterfaceAlias` (Namen), den Sie für fast alle anderen Befehle benötigen. |
| `Get-NetIPConfiguration` | Zeigt die aktuelle IP-Konfiguration (IP, Gateway, DNS) ähnlich dem alten `ipconfig`. | `-InterfaceAlias "Ethernet"`: Filtert die Anzeige nach einem bestimmten Adapter.<br>`-Detailed`: Zeigt erweiterte Details. |
| `Get-NetIPAddress` | Listet alle auf dem System konfigurierten IP-Adressen (IPv4 und IPv6) detailliert auf. | `-AddressFamily IPv4`: Zeigt nur IPv4-Adressen.<br>`-InterfaceAlias "Wi-Fi"`: Filtert nach Adapter. |
| `Get-DnsClientServerAddress` | Zeigt die aktuell zugewiesenen DNS-Serveradressen für alle Adapter an. | *Keine Parameter nötig.* |

---

### ⚙️ IP-Adresse & DNS konfigurieren

| Befehl | Was macht dieser Command? | Wichtige Parameter & Erklärung |
| :--- | :--- | :--- |
| `New-NetIPAddress` | Weist einer Netzwerkschnittstelle eine **neue statische IP-Adresse** zu. | `-InterfaceAlias "Name"`: Name des Adapters.<br>`-IPAddress "192.168.1.50"`: Die gewünschte IP.<br>`-PrefixLength 24`: Subnetzmaske (24 entspricht 255.255.255.0).<br>`-DefaultGateway "192.168.1.1"`: Das Standard-Gateway. |
| `Remove-NetIPAddress` | Löscht eine bestehende IP-Adresse von einem Adapter. | `-InterfaceAlias "Name"`: Ziel-Adapter.<br>`-IPAddress "192.168.1.50"`: Spezifische IP zum Löschen.<br>`-Confirm:$false`: Unterdrückt die Bestätigungsaufforderung. |
| `Set-DnsClientServerAddress` | Konfiguriert die **DNS-Server** für einen bestimmten Adapter. | `-InterfaceAlias "Name"`: Ziel-Adapter.<br>`-ServerAddresses ("1.1.1.1","8.8.8.8")`: Array von DNS-Servern (kommagetrennt). |
| `Set-NetIPInterface` | Ändert Schnittstellen-Eigenschaften, z.B. das Umschalten auf **DHCP**. | `-InterfaceAlias "Name"`: Ziel-Adapter.<br>`-Dhcp Enabled`: Aktiviert den automatischen IP-Bezug.<br>`-Dhcp Disabled`: Deaktiviert DHCP (für statische IPs). |

> [!TIP]
> **Zurücksetzen auf DHCP (Automatische IP)**
> Wenn Sie von einer statischen IP zurück zu DHCP wechseln wollen, nutzen Sie diese Kombination:
> ```powershell
> Set-NetIPInterface -InterfaceAlias "Ethernet" -Dhcp Enabled
> Set-DnsClientServerAddress -InterfaceAlias "Ethernet" -ResetServerAddresses
> ```

---

### 🌐 Diagnose & Fehlerbehebung

| Befehl | Was macht dieser Command? | Wichtige Parameter & Erklärung |
| :--- | :--- | :--- |
| `Test-Connection` | Das moderne Gegenstück zu `ping`. Prüft die Erreichbarkeit eines Hosts. | `-TargetName "google.com"`: Das Ziel (IP oder Domain).<br>`-Count 4`: Anzahl der gesendeten Ping-Anfragen.<br>`-Quiet`: Gibt nur `True` oder `False` zurück (ideal für Skripte). |
| `Test-NetConnection` | Ein mächtiges Diagnose-Werkzeug (kombiniert Ping, Traceroute und Port-Scanner). | `-ComputerName "example.com"`: Zieladresse.<br>`-Port 443`: Prüft, ob ein spezifischer TCP-Port offen ist.<br>`-TraceRoute`: Führt eine Routenverfolgung (wie `tracert`) durch. |
| `Clear-DnsClientCache` | Leert den lokalen DNS-Resolver-Cache (entspricht `ipconfig /flushdns`). | *Keine Parameter nötig.* Hilft, wenn Webseiten nach Domain-Änderungen nicht laden. |
| `Resolve-DnsName` | Führt eine DNS-Abfrage für eine Domain durch (modernes `nslookup`). | `-Name "microsoft.com"`: Abzufragende Domain.<br>`-Type MX`: Fragt spezifische DNS-Records ab (z.B. A, AAAA, MX, TXT). |

---

### 🔓 Skriptausführung erlauben

Wenn Sie `.ps1`-Skripte lokal ausführen möchten, blockiert Windows dies oft standardmäßig.

| Befehl | Was macht dieser Command? | Wichtige Parameter & Erklärung |
| :--- | :--- | :--- |
| `Set-ExecutionPolicy` | Ändert die Sicherheitsrichtlinie für die Ausführung von PowerShell-Skripten. | `-ExecutionPolicy Bypass`: Erlaubt die Ausführung aller Skripte temporär ohne Warnung.<br>`-Scope Process`: Gilt nur für das aktuell geöffnete PowerShell-Fenster (sicherste Variante). |
