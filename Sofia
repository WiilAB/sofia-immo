import { useState, useRef, useEffect } from "react";

const SOFIA_SYSTEM_PROMPT = `Tu es Sofia, une assistante IA spécialisée dans l'immobilier à Besançon et dans la région de Franche-Comté. Tu travailles pour une agence immobilière locale.

Ton rôle est de qualifier les prospects qui contactent l'agence. Tu dois :
1. Accueillir chaleureusement le prospect
2. Comprendre leur projet (achat, vente, location, investissement)
3. Collecter les informations clés : budget, type de bien recherché, délai, situation personnelle (primo-accédant, investisseur, etc.)
4. Évaluer leur niveau de maturité (juste curieux vs prêt à signer)
5. Si le prospect est qualifié, proposer un rendez-vous avec un conseiller

Tu es professionnelle, chaleureuse et efficace. Tu poses une question à la fois pour ne pas submerger le prospect. Tu connais bien le marché immobilier de Besançon : prix au m², quartiers (Battant, Planoise, Centre-ville, Saint-Ferjeux, Palente...), et les spécificités locales.

Commence toujours par te présenter et demander comment tu peux aider.

Réponds toujours en français. Sois concise (2-3 phrases max par réponse).`;

const formatTime = () => {
  const now = new Date();
  return now.toLocaleTimeString("fr-FR", { hour: "2-digit", minute: "2-digit" });
};

export default function Sofia() {
  const [messages, setMessages] = useState([]);
  const [input, setInput] = useState("");
  const [loading, setLoading] = useState(false);
  const [started, setStarted] = useState(false);
  const messagesEndRef = useRef(null);

  useEffect(() => {
    messagesEndRef.current?.scrollIntoView({ behavior: "smooth" });
  }, [messages]);

  const startConversation = async () => {
    setStarted(true);
    setLoading(true);
    try {
      const response = await fetch("https://api.anthropic.com/v1/messages", {
        method: "POST",
        headers: { "Content-Type": "application/json" },
        body: JSON.stringify({
          model: "claude-sonnet-4-6",
          max_tokens: 1000,
          system: SOFIA_SYSTEM_PROMPT,
          messages: [{ role: "user", content: "Bonjour" }],
        }),
      });
      const data = await response.json();
      const text = data.content?.[0]?.text || "Bonjour ! Je suis Sofia.";
      setMessages([{ role: "assistant", content: text, time: formatTime() }]);
    } catch {
      setMessages([{ role: "assistant", content: "Bonjour ! Je suis Sofia, votre assistante immobilière. Comment puis-je vous aider aujourd'hui ?", time: formatTime() }]);
    }
    setLoading(false);
  };

  const sendMessage = async () => {
    if (!input.trim() || loading) return;
    const userMsg = { role: "user", content: input.trim(), time: formatTime() };
    const newMessages = [...messages, userMsg];
    setMessages(newMessages);
    setInput("");
    setLoading(true);
    try {
      const apiMessages = newMessages.map((m) => ({ role: m.role, content: m.content }));
      const response = await fetch("https://api.anthropic.com/v1/messages", {
        method: "POST",
        headers: { "Content-Type": "application/json" },
        body: JSON.stringify({
          model: "claude-sonnet-4-6",
          max_tokens: 1000,
          system: SOFIA_SYSTEM_PROMPT,
          messages: apiMessages,
        }),
      });
      const data = await response.json();
      const text = data.content?.[0]?.text || "Je n'ai pas bien compris, pouvez-vous reformuler ?";
      setMessages((prev) => [...prev, { role: "assistant", content: text, time: formatTime() }]);
    } catch {
      setMessages((prev) => [...prev, { role: "assistant", content: "Désolée, une erreur s'est produite. Veuillez réessayer.", time: formatTime() }]);
    }
    setLoading(false);
  };

  const handleKey = (e) => {
    if (e.key === "Enter" && !e.shiftKey) { e.preventDefault(); sendMessage(); }
  };

  return (
    <div style={{ minHeight: "100vh", background: "linear-gradient(135deg, #0f1923 0%, #1a2d3d 50%, #0f1923 100%)", display: "flex", alignItems: "center", justifyContent: "center", fontFamily: "'Inter', -apple-system, sans-serif", padding: "20px" }}>
      <div style={{ width: "100%", maxWidth: "440px", display: "flex", flexDirection: "column" }}>
        <div style={{ background: "linear-gradient(135deg, #1e3a5f, #2563a8)", borderRadius: "20px 20px 0 0", padding: "24px", display: "flex", alignItems: "center", gap: "14px" }}>
          <div style={{ position: "relative" }}>
            <div style={{ width: "52px", height: "52px", borderRadius: "50%", background: "linear-gradient(135deg, #60a5fa, #3b82f6)", display: "flex", alignItems: "center", justifyContent: "center", fontSize: "22px" }}>🏠</div>
            <div style={{ position: "absolute", bottom: 2, right: 2, width: "12px", height: "12px", background: "#22c55e", borderRadius: "50%", border: "2px solid #1e3a5f" }} />
          </div>
          <div>
            <div style={{ color: "#fff", fontWeight: 700, fontSize: "18px" }}>Sofia</div>
            <div style={{ color: "#93c5fd", fontSize: "13px" }}>Assistante Immobilière IA · Besançon</div>
          </div>
          <div style={{ marginLeft: "auto", background: "rgba(255,255,255,0.1)", borderRadius: "20px", padding: "4px 12px", color: "#93c5fd", fontSize: "12px" }}>🟢 En ligne</div>
        </div>
        <div style={{ background: "#f8fafc", height: "420px", overflowY: "auto", padding: "20px 16px", display: "flex", flexDirection: "column", gap: "12px" }}>
          {!started && (
            <div style={{ display: "flex", flexDirection: "column", alignItems: "center", justifyContent: "center", height: "100%", gap: "20px", textAlign: "center" }}>
              <div style={{ fontSize: "48px" }}>🏡</div>
              <div>
                <div style={{ color: "#1e3a5f", fontWeight: 700, fontSize: "18px", marginBottom: "8px" }}>Bienvenue chez votre agence</div>
                <div style={{ color: "#64748b", fontSize: "14px", lineHeight: "1.5" }}>Sofia va qualifier votre projet immobilier et vous mettre en relation avec un conseiller</div>
              </div>
              <button onClick={startConversation} style={{ background: "linear-gradient(135deg, #2563a8, #1d4ed8)", color: "#fff", border: "none", borderRadius: "25px", padding: "14px 32px", fontSize: "15px", fontWeight: 600, cursor: "pointer" }}>Démarrer la conversation</button>
            </div>
          )}
          {messages.map((msg, i) => (
            <div key={i} style={{ display: "flex", flexDirection: msg.role === "user" ? "row-reverse" : "row", alignItems: "flex-end", gap: "8px" }}>
              {msg.role === "assistant" && <div style={{ width: "32px", height: "32px", borderRadius: "50%", background: "linear-gradient(135deg, #60a5fa, #3b82f6)", display: "flex", alignItems: "center", justifyContent: "center", fontSize: "14px" }}>🏠</div>}
              <div style={{ maxWidth: "75%" }}>
                <div style={{ background: msg.role === "user" ? "linear-gradient(135deg, #2563a8, #1d4ed8)" : "#fff", color: msg.role === "user" ? "#fff" : "#1e293b", borderRadius: msg.role === "user" ? "18px 18px 4px 18px" : "18px 18px 18px 4px", padding: "12px 16px", fontSize: "14px", lineHeight: "1.5", boxShadow: "0 2px 8px rgba(0,0,0,0.08)" }}>{msg.content}</div>
                <div style={{ color: "#94a3b8", fontSize: "11px", marginTop: "4px", textAlign: msg.role === "user" ? "right" : "left" }}>{msg.time}</div>
              </div>
            </div>
          ))}
          {loading && (
            <div style={{ display: "flex", alignItems: "flex-end", gap: "8px" }}>
              <div style={{ width: "32px", height: "32px", borderRadius: "50%", background: "linear-gradient(135deg, #60a5fa, #3b82f6)", display: "flex", alignItems: "center", justifyContent: "center", fontSize: "14px" }}>🏠</div>
              <div style={{ background: "#fff", borderRadius: "18px 18px 18px 4px", padding: "14px 18px", boxShadow: "0 2px 8px rgba(0,0,0,0.08)", display: "flex", gap: "5px" }}>
                {[0,1,2].map(i => <div key={i} style={{ width: "7px", height: "7px", borderRadius: "50%", background: "#93c5fd", animation: `bounce 1.2s ease-in-out ${i*0.2}s infinite` }} />)}
              </div>
            </div>
          )}
          <div ref={messagesEndRef} />
        </div>
        <div style={{ background: "#fff", borderRadius: "0 0 20px 20px", padding: "16px", display: "flex", gap: "10px", alignItems: "center", borderTop: "1px solid #e2e8f0" }}>
          <input value={input} onChange={e => setInput(e.target.value)} onKeyDown={handleKey} placeholder={started ? "Écrivez votre message..." : "Démarrez la conversation d'abord"} disabled={!started || loading} style={{ flex: 1, border: "1px solid #e2e8f0", borderRadius: "25px", padding: "12px 18px", fontSize: "14px", outline: "none", background: started ? "#f8fafc" : "#f1f5f9", color: "#1e293b" }} />
          <button onClick={sendMessage} disabled={!started || loading || !input.trim()} style={{ width: "44px", height: "44px", borderRadius: "50%", background: input.trim() && started ? "linear-gradient(135deg, #2563a8, #1d4ed8)" : "#e2e8f0", border: "none", cursor: "pointer", display: "flex", alignItems: "center", justifyContent: "center", fontSize: "18px" }}>➤</button>
        </div>
      </div>
      <style>{`@keyframes bounce { 0%, 60%, 100% { transform: translateY(0); } 30% { transform: translateY(-6px); } }`}</style>
    </div>
  );
}
