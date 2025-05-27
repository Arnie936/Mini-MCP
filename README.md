# MCP Taschenrechner-Server

Dieses Projekt zeigt, wie du mit dem MCP Python SDK einen einfachen MCP-Server erstellst, der eine Taschenrechner-Funktion (Addition) als Tool bereitstellt. Der Server kann mit jedem MCP-kompatiblen Client (z.B. Claude Desktop) verwendet werden.

## Features

- **add**: Addiert zwei Zahlen und gibt das Ergebnis zurück.

---

## Voraussetzungen

- **Python 3.9+**  
  Prüfe deine Python-Version mit:
  ```sh
  python --version
  ```
  oder
  ```sh
  python3 --version
  ```

- **uv** (moderner Python-Paketmanager)  
  Prüfe, ob `uv` installiert ist:
  ```sh
  uv --version
  ```
  Falls nicht vorhanden, installiere es:
  ```sh
  pip install uv
  ```

---

## Installation

1. **Wechsle in deinen Projektordner**  
   (Du hast den Ordner bereits angelegt und geöffnet.)

2. **Initialisiere das Projekt mit uv**  
   ```sh
   uv init .
   ```

3. **Installiere MCP (inkl. CLI) als Abhängigkeit**  
   ```sh
   uv add "mcp[cli]"
   ```

4. **Projektstruktur anlegen**  
   Erstelle die Datei für den Server:
   ```sh
   new-item server.py
   ```
   (oder per Editor anlegen)

---

## Beispielcode: server.py

```python
from mcp.server.fastmcp import FastMCP

# MCP-Server initialisieren
mcp = FastMCP("Taschenrechner")

# Tool: Addition
@mcp.tool()
def add(a: int, b: int) -> int:
    """Addiert zwei Zahlen"""
    return a + b

if __name__ == "__main__":
    mcp.run()
```

---

## Server starten & testen

### 1. Starte den Server im Entwicklungsmodus

```sh
uv run mcp dev server.py
```

- Der Server läuft jetzt und kann von einem MCP-Client (z.B. Claude Desktop) erkannt werden.

### 2. Funktion prüfen

- Öffne Claude Desktop oder einen anderen MCP-Client.
- Füge den Server hinzu (z.B. über die Datei `server.py`).
- Teste das Tool `add` mit zwei Zahlen.

---

## Fehlerbehebung

- **Python nicht gefunden:**  
  Prüfe, ob Python korrekt installiert und im PATH ist.
- **uv nicht gefunden:**  
  Installiere uv mit `pip install uv`.
- **MCP-Server wird nicht erkannt:**  
  Stelle sicher, dass der Server läuft und die Datei korrekt angegeben ist.

---

## Weiterführende Links

- [MCP Python SDK (GitHub)](https://github.com/modelcontextprotocol/python-sdk)
- [Offizielle MCP-Dokumentation](https://modelcontextprotocol.io/)

---

**Hinweis:**  
Dieses Beispiel ist minimal gehalten. Du kannst weitere Tools oder Ressourcen nach Bedarf ergänzen. 