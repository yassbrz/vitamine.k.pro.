<!DOCTYPE html>
<html lang="fr">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Expert Vitamine K</title>
    <style>
        body { font-family: sans-serif; background: #121212; color: white; text-align: center; padding: 20px; }
        .card { max-width: 400px; margin: auto; background: #1e1e1e; padding: 20px; border-radius: 20px; border: 1px solid #333; }
        .btn { background: #2ecc71; color: white; border: none; padding: 15px; border-radius: 10px; width: 100%; font-weight: bold; margin-bottom: 10px; cursor: pointer; }
        input[type="text"] { width: 90%; padding: 12px; border-radius: 10px; border: 1px solid #444; background: #2a2a2a; color: white; margin-bottom: 10px; }
        #resultat { margin-top: 20px; padding: 15px; background: #2a2a2a; border-radius: 10px; text-align: left; border-left: 4px solid #2ecc71; }
        #preview { width: 100%; border-radius: 10px; display: none; margin-top: 10px; }
    </style>
</head>
<body>
    <div class="card">
        <h1>🥬 Expert Vitamine K</h1>
        
        <input type="text" id="searchQuery" placeholder="Ex: Épinards, Poulet...">
        <button class="btn" onclick="analyserTexte()">🔍 RECHERCHER</button>
        
        <div style="margin: 15px 0; color: #555;">— OU —</div>

        <button class="btn" style="background: #3498db;" onclick="document.getElementById('p').click()">📸 PRENDRE UNE PHOTO</button>
        <input type="file" id="p" accept="image/*" capture="environment" style="display:none" onchange="runPhoto(event)">
        
        <div id="ld" style="display:none; color:yellow;">⌛ Analyse en cours...</div>
        <img id="preview">
        <div id="resultat">Entrez un aliment ou prenez une photo.</div>
    </div>

<script>
    const KEY = "AIzaSyC2OLQp6L3kMNCI1V3nw7bWDkc03Kvw_yE";

    async function appelerIA(contenu) {
        document.getElementById('ld').style.display = 'block';
        try {
            const r = await fetch(`https://generativelanguage.googleapis.com/v1beta/models/gemini-1.5-flash:generateContent?key=${KEY}`, {
                method: 'POST',
                headers: {'Content-Type': 'application/json'},
                body: JSON.stringify({contents: [{parts: contenu}]})
            });
            const d = await r.json();
            document.getElementById('resultat').innerHTML = d.candidates[0].content.parts[0].text.replace(/\n/g, "<br>");
        } catch (err) {
            document.getElementById('resultat').innerText = "Erreur de connexion avec l'IA.";
        }
        document.getElementById('ld').style.display = 'none';
    }

    function analyserTexte() {
        const q = document.getElementById('searchQuery').value;
        if(!q) return;
        appelerIA([{text: `Donne le taux de Vitamine K pour 100g de l'aliment suivant : ${q}. Réponds en français de manière claire.`}]);
    }

    async function runPhoto(e) {
        const file = e.target.files[0];
        const reader = new FileReader();
        reader.onload = async (ex) => {
            document.getElementById('preview').src = ex.target.result;
            document.getElementById('preview').style.display = 'block';
            const base64 = ex.target.result.split(',')[1];
            appelerIA([
                {text: "Identifie cet aliment et donne son taux de Vitamine K pour 100g en français."},
                {inline_data: {mime_type: "image/jpeg", data: base64}}
            ]);
        };
        reader.readAsDataURL(file);
    }
</script>
</body>
</html>
