# Escape-Game
<!DOCTYPE html>
<html lang="de">
<head>
<meta charset="UTF-8" />
<meta name="viewport" content="width=device-width, initial-scale=1.0" />
<title>Schnitzeljagd App</title>
<script src="https://unpkg.com/html5-qrcode@2.2.1/minified/html5-qrcode.min.js"></script>
<style>
  body { font-family: Arial, sans-serif; margin: 20px; background-color: #f0f8ff; }
  h1 { text-align: center; color: #006400; }
  #reader { width: 300px; margin: auto; }
  #status { margin-top: 20px; font-size: 1.2em; text-align: center; }
  #hint { margin-top: 20px; padding: 10px; border: 2px solid #006400; border-radius: 8px; min-height: 50px; background-color: #fff; }
  button { display: block; margin: 20px auto; padding: 10px 20px; font-size: 1em; border: none; border-radius: 5px; background-color: #006400; color: #fff; cursor: pointer; }
  button:hover { background-color: #004d00; }
</style>
</head>
<body>

<h1>Schnitzeljagd</h1>
<div id="reader"></div>
<div id="status">Bitte scanne den QR-Code, um zu starten.</div>
<div id="hint"></div>
<button onclick="startScanning()">QR-Code scannen starten</button>

<script>
// Stationen-Daten
const stations = [
  {
    id: "station1",
    hint: "Finde den Baum mit der alten Eiche!",
    next: "station2"
  },
  {
    id: "station2",
    hint: "Suche den Stein mit der eingemeißelten Zahl 7.",
    next: "station3"
  },
  {
    id: "station3",
    hint: "Gibt es eine versteckte Truhe im kleinen Wasserbecken?",
    next: null
  }
];

let currentStationIndex = 0;
let html5QrCode;

function showHint(text) {
  document.getElementById("hint").innerText = text;
}

function startScanning() {
  document.getElementById("status").innerText = "Kamera wird gestartet... Bitte erlauben Sie den Zugriff.";
  if (html5QrCode) {
    html5QrCode.stop().then(() => {
      html5QrCode.clear();
      initScanner();
    }).catch((err) => {
      console.error(err);
      alert("Fehler beim Stoppen des Scanners");
    });
  } else {
    initScanner();
  }
}

function initScanner() {
  html5QrCode = new Html5Qrcode("reader");
  const config = { fps: 10, qrbox: 250 };
  
  html5QrCode.start({ facingMode: "environment" }, config, (decodedText, decodedResult) => {
    const station = stations[currentStationIndex];
    if (decodedText === station.id) {
      showHint(station.hint);
      document.getElementById("status").innerText = "Station gefunden!";
      if (station.next) {
        currentStationIndex++;
        alert("Super! Weiter zum nächsten Hinweis.");
      } else {
        alert("Herzlichen Glückwunsch! Du hast die Schnitzeljagd beendet!");
        html5QrCode.stop();
        document.getElementById("status").innerText = "Jagd beendet.";
      }
    } else {
      alert("Falscher QR-Code! Bitte erneut scannen.");
    }
  }).catch((err) => {
    console.error(err);
    alert("Fehler beim Starten der Kamera");
  });
}
</script>

</body>
</html>
