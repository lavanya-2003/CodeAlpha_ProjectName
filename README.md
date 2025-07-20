# CodeAlpha_ProjectName
CodeAlpha_
import React, { useState } from "react";

// For demo, using Microsoft Translator API
const API_KEY = "YOUR_MICROSOFT_TRANSLATOR_API_KEY";
const ENDPOINT = "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0";

// List of supported languages (partial, add more as needed)
const LANGUAGES = [
  { code: "en", name: "English" },
  { code: "es", name: "Spanish" },
  { code: "fr", name: "French" },
  { code: "de", name: "German" },
  { code: "hi", name: "Hindi" },
  { code: "zh-Hans", name: "Chinese (Simplified)" },
  { code: "ja", name: "Japanese" },
];

function TranslationApp() {
  const [text, setText] = useState("");
  const [source, setSource] = useState("en");
  const [target, setTarget] = useState("es");
  const [translated, setTranslated] = useState("");
  const [loading, setLoading] = useState(false);
  const [error, setError] = useState("");

  const translate = async () => {
    setLoading(true);
    setError("");
    setTranslated("");
    try {
      const url = `${ENDPOINT}&from=${source}&to=${target}`;
      const response = await fetch(url, {
        method: "POST",
        headers: {
          "Ocp-Apim-Subscription-Key": API_KEY,
          "Ocp-Apim-Subscription-Region": "YOUR_RESOURCE_REGION",
          "Content-Type": "application/json",
        },
        body: JSON.stringify([{ Text: text }]),
      });
      const data = await response.json();
      if (response.ok) {
        setTranslated(data[0]?.translations[0]?.text || "");
      } else {
        setError(data.error?.message || "Translation failed.");
      }
    } catch (e) {
      setError("Network error.");
    }
    setLoading(false);
  };

  const copyToClipboard = () => {
    navigator.clipboard.writeText(translated);
  };

  const speak = () => {
    if ("speechSynthesis" in window) {
      const utter = new SpeechSynthesisUtterance(translated);
      utter.lang = target;
      window.speechSynthesis.speak(utter);
    }
  };

  return (
    <div style={{ maxWidth: 500, margin: "2rem auto", padding: 24, border: "1px solid #ddd", borderRadius: 8 }}>
      <h2>Text Translator</h2>
      <textarea
        placeholder="Enter text here"
        value={text}
        onChange={e => setText(e.target.value)}
        rows={4}
        style={{ width: "100%", marginBottom: 8 }}
      />
      <div style={{ display: "flex", gap: 8, marginBottom: 8 }}>
        <label>
          From:
          <select value={source} onChange={e => setSource(e.target.value)}>
            {LANGUAGES.map(lang => (
              <option key={lang.code} value={lang.code}>{lang.name}</option>
            ))}
          </select>
        </label>
        <label>
          To:
          <select value={target} onChange={e => setTarget(e.target.value)}>
            {LANGUAGES.map(lang => (
              <option key={lang.code} value={lang.code}>{lang.name}</option>
            ))}
          </select>
        </label>
      </div>
      <button onClick={translate} disabled={!text || loading} style={{ width: "100%" }}>
        {loading ? "Translating..." : "Translate"}
      </button>
      {error && <div style={{ color: "red", marginTop: 8 }}>{error}</div>}
      {translated && (
        <div style={{ marginTop: 16, padding: 12, background: "#f9f9f9", borderRadius: 6 }}>
          <div><b>Translated:</b></div>
          <div style={{ margin: "8px 0", fontSize: 18 }}>{translated}</div>
          <button onClick={copyToClipboard} style={{ marginRight: 8 }}>Copy</button>
          <button onClick={speak}>Speak</button>
        </div>
      )}
    </div>
  );
}

export default TranslationApp;
