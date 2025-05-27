# Mini-MCP

Dieses Projekt zeigt, wie du mit dem MCP Python SDK einen MCP-Server erstellst, der verschiedene Funktionen bereitstellt: Tools (wie eine Taschenrechner-Funktion), Resources (zum Bereitstellen von Dateiinhalten) und Prompt Templates (für strukturierte Ausgaben). Der Server kann mit jedem MCP-kompatiblen Client (z.B. Claude Desktop) verwendet werden.

## Features

- **add**: Addiert zwei Zahlen und gibt das Ergebnis zurück.
- **file://pysdk.md**: Resource zum Bereitstellen von Dateiinhalten (pysdk.md vom Desktop)
- **meeting_prompt**: Prompt Template für Besprechungszusammenfassungen basierend auf meeting_prompt.md

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
from mcp.server.fastmcp import FastMCP, Context
import os
import asyncio
from pathlib import Path

# MCP-Server initialisieren
mcp = FastMCP(
    "Taschenrechner",
    host=os.getenv("HOST", "localhost"),
    port=int(os.getenv("PORT", "3000"))
)

# Tool: Addition
@mcp.tool()
def add(a: int, b: int) -> int:
    """Addiert zwei Zahlen"""
    return a + b

# Resource: Inhalt von pysdk.md bereitstellen
@mcp.resource("file://pysdk.md")
async def pysdkfile() -> str:
    desktop_path = Path.home() / "Desktop"
    file_path = desktop_path / "pysdk.md"
    try:
        with open(file_path, "r", encoding="utf-8") as file:
            return file.read()
    except Exception as e:
        return f"Error reading pysdk.md: {str(e)}"

# MCP Prompt: meeting_prompt
@mcp.prompt()
def meeting_prompt(meeting_date: str, meeting_title: str, transcript: str) -> str:
    """Erstellt eine Besprechungszusammenfassung basierend auf dem Template aus meeting_prompt.md."""
    template_path = Path(__file__).parent / "meeting_prompt.md"
    try:
        with open(template_path, "r", encoding="utf-8") as f:
            template = f.read()
    except Exception as e:
        return f"Fehler beim Laden des Templates: {str(e)}"
    # Template-Variablen ersetzen
    result = template.replace("{{ meeting_date }}", meeting_date)
    result = result.replace("{{ meeting_title }}", meeting_title)
    result = result.replace("{{ transcript }}", transcript)
    return result

async def main():
    transport = os.getenv("TRANSPORT", "stdio")
    if transport == 'sse':
        await mcp.run_sse_async()
    else:
        await mcp.run_stdio_async()

if __name__ == "__main__":
    asyncio.run(main())
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
