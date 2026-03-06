# Ganesh_Groups
import { useState, useEffect, useRef } from "react";

// ============================================================
// VYJ GROUPS - AI CUSTOMS AUTOMATION PLATFORM
// Full-stack React SPA with live Claude AI integration
// ============================================================

const COLORS = {
  navy: "#0A1628",
  navyMid: "#0D1F3C",
  navyLight: "#152544",
  blue: "#1565C0",
  blueMid: "#1976D2",
  blueLight: "#2196F3",
  blueAccent: "#42A5F5",
  green: "#00897B",
  greenMid: "#00ACC1",
  greenBright: "#26C6DA",
  greenGlow: "#00BCD4",
  white: "#F0F4FF",
  whiteDim: "#B8C5E0",
  border: "#1E3A5F",
  card: "#0F2035",
  cardHover: "#132843",
  danger: "#EF5350",
  warning: "#FFA726",
  success: "#66BB6A",
};

// ============================================================
// LOGO COMPONENT
// ============================================================
function VYJLogo({ size = 40, showText = true }) {
  return (
    <div style={{ display: "flex", alignItems: "center", gap: 10 }}>
      <svg width={size} height={size} viewBox="0 0 40 40">
        <defs>
          <linearGradient id="logoGrad" x1="0%" y1="0%" x2="100%" y2="100%">
            <stop offset="0%" stopColor="#00BCD4" />
            <stop offset="50%" stopColor="#1976D2" />
            <stop offset="100%" stopColor="#26C6DA" />
          </linearGradient>
          <linearGradient id="logoGrad2" x1="0%" y1="0%" x2="100%" y2="100%">
            <stop offset="0%" stopColor="#00897B" />
            <stop offset="100%" stopColor="#1565C0" />
          </linearGradient>
        </defs>
        <polygon points="20,2 38,11 38,29 20,38 2,29 2,11" fill="url(#logoGrad2)" opacity="0.2" />
        <polygon points="20,2 38,11 38,29 20,38 2,29 2,11" fill="none" stroke="url(#logoGrad)" strokeWidth="1.5" />
        <text x="20" y="26" textAnchor="middle" fill="url(#logoGrad)" fontSize="14" fontWeight="800" fontFamily="Georgia, serif">V</text>
        <circle cx="32" cy="10" r="3" fill="#00BCD4" opacity="0.8" />
        <circle cx="8" cy="10" r="2" fill="#1976D2" opacity="0.6" />
        <line x1="8" y1="10" x2="32" y2="10" stroke="url(#logoGrad)" strokeWidth="0.8" opacity="0.4" />
      </svg>
      {showText && (
        <div>
          <div style={{ fontSize: size * 0.38, fontWeight: 800, letterSpacing: "0.12em", background: "linear-gradient(90deg, #00BCD4, #1976D2, #26C6DA)", WebkitBackgroundClip: "text", WebkitTextFillColor: "transparent", fontFamily: "Georgia, serif", lineHeight: 1 }}>
            VYJ GROUPS
          </div>
          <div style={{ fontSize: size * 0.22, color: COLORS.whiteDim, letterSpacing: "0.18em", fontFamily: "monospace", lineHeight: 1.2 }}>
            CUSTOMS AI
          </div>
        </div>
      )}
    </div>
  );
}

// ============================================================
// SHARED UI COMPONENTS
// ============================================================
function Card({ children, style = {}, glow = false }) {
  return (
    <div style={{
      background: COLORS.card,
      border: `1px solid ${glow ? COLORS.greenGlow : COLORS.border}`,
      borderRadius: 12,
      padding: "20px 24px",
      boxShadow: glow ? `0 0 20px rgba(0,188,212,0.15)` : "0 4px 20px rgba(0,0,0,0.3)",
      ...style
    }}>
      {children}
    </div>
  );
}

function Badge({ children, color = "blue" }) {
  const colors = {
    blue: { bg: "rgba(33,150,243,0.15)", text: "#42A5F5", border: "rgba(33,150,243,0.3)" },
    green: { bg: "rgba(0,188,212,0.15)", text: "#26C6DA", border: "rgba(0,188,212,0.3)" },
    danger: { bg: "rgba(239,83,80,0.15)", text: "#EF9A9A", border: "rgba(239,83,80,0.3)" },
    warning: { bg: "rgba(255,167,38,0.15)", text: "#FFB74D", border: "rgba(255,167,38,0.3)" },
    success: { bg: "rgba(102,187,106,0.15)", text: "#81C784", border: "rgba(102,187,106,0.3)" },
  };
  const c = colors[color] || colors.blue;
  return (
    <span style={{ background: c.bg, color: c.text, border: `1px solid ${c.border}`, borderRadius: 6, padding: "2px 10px", fontSize: 11, fontWeight: 700, letterSpacing: "0.08em", fontFamily: "monospace" }}>
      {children}
    </span>
  );
}

function StatCard({ icon, label, value, delta, color = COLORS.blueAccent }) {
  return (
    <Card style={{ flex: 1, minWidth: 160 }}>
      <div style={{ display: "flex", justifyContent: "space-between", alignItems: "flex-start" }}>
        <div>
          <div style={{ color: COLORS.whiteDim, fontSize: 11, letterSpacing: "0.1em", fontFamily: "monospace", marginBottom: 8 }}>{label}</div>
          <div style={{ color: COLORS.white, fontSize: 28, fontWeight: 800, fontFamily: "Georgia, serif" }}>{value}</div>
          {delta && <div style={{ color: COLORS.success, fontSize: 11, marginTop: 4 }}>↑ {delta}</div>}
        </div>
        <div style={{ width: 44, height: 44, borderRadius: 10, background: `${color}22`, border: `1px solid ${color}44`, display: "flex", alignItems: "center", justifyContent: "center", fontSize: 20 }}>{icon}</div>
      </div>
    </Card>
  );
}

function Spinner() {
  return (
    <div style={{ display: "flex", alignItems: "center", gap: 8 }}>
      <div style={{
        width: 16, height: 16,
        border: `2px solid ${COLORS.border}`,
        borderTop: `2px solid ${COLORS.greenGlow}`,
        borderRadius: "50%",
        animation: "spin 0.8s linear infinite"
      }} />
      <span style={{ color: COLORS.whiteDim, fontSize: 13 }}>AI Processing...</span>
    </div>
  );
}

function Button({ children, onClick, variant = "primary", disabled = false, style = {} }) {
  const variants = {
    primary: { background: "linear-gradient(135deg, #1565C0, #00897B)", color: "#fff", border: "none" },
    secondary: { background: "transparent", color: COLORS.blueAccent, border: `1px solid ${COLORS.blueAccent}` },
    danger: { background: "rgba(239,83,80,0.15)", color: "#EF9A9A", border: "1px solid rgba(239,83,80,0.4)" },
    success: { background: "linear-gradient(135deg, #00897B, #1976D2)", color: "#fff", border: "none" },
  };
  return (
    <button onClick={onClick} disabled={disabled} style={{
      ...variants[variant],
      padding: "10px 20px",
      borderRadius: 8,
      fontSize: 13,
      fontWeight: 700,
      letterSpacing: "0.06em",
      cursor: disabled ? "not-allowed" : "pointer",
      opacity: disabled ? 0.5 : 1,
      transition: "all 0.2s",
      fontFamily: "monospace",
      ...style
    }}>
      {children}
    </button>
  );
}

// ============================================================
// AI CALL HELPER
// ============================================================
async function callClaudeAI(prompt, systemPrompt = "") {
  const response = await fetch("https://api.anthropic.com/v1/messages", {
    method: "POST",
    headers: { "Content-Type": "application/json" },
    body: JSON.stringify({
      model: "claude-sonnet-4-20250514",
      max_tokens: 1000,
      system: systemPrompt || "You are an expert customs classification AI assistant. Return ONLY valid JSON, no markdown.",
      messages: [{ role: "user", content: prompt }]
    })
  });
  const data = await response.json();
  const text = data.content?.map(i => i.text || "").join("") || "";
  const clean = text.replace(/```json|```/g, "").trim();
  try { return JSON.parse(clean); } catch { return { raw: text }; }
}

// ============================================================
// PAGE: LOGIN / REGISTER
// ============================================================
function LoginPage({ onLogin }) {
  const [mode, setMode] = useState("login");
  const [form, setForm] = useState({ email: "", password: "", company: "" });
  const [loading, setLoading] = useState(false);

  const handleSubmit = () => {
    setLoading(true);
    setTimeout(() => { setLoading(false); onLogin({ email: form.email, company: form.company || "VYJ Groups" }); }, 1200);
  };

  return (
    <div style={{ minHeight: "100vh", background: COLORS.navy, display: "flex", alignItems: "center", justifyContent: "center", fontFamily: "monospace", position: "relative", overflow: "hidden" }}>
      {/* Background grid */}
      <div style={{ position: "absolute", inset: 0, backgroundImage: "linear-gradient(rgba(21,101,192,0.05) 1px, transparent 1px), linear-gradient(90deg, rgba(21,101,192,0.05) 1px, transparent 1px)", backgroundSize: "40px 40px" }} />
      <div style={{ position: "absolute", top: -200, right: -200, width: 600, height: 600, borderRadius: "50%", background: "radial-gradient(circle, rgba(0,188,212,0.06) 0%, transparent 70%)" }} />

      <div style={{ position: "relative", width: 420, zIndex: 1 }}>
        <div style={{ textAlign: "center", marginBottom: 36 }}>
          <VYJLogo size={56} />
          <div style={{ color: COLORS.whiteDim, fontSize: 12, marginTop: 12, letterSpacing: "0.15em" }}>
            AI-POWERED CUSTOMS AUTOMATION
          </div>
        </div>

        <Card glow>
          <div style={{ display: "flex", borderRadius: 8, overflow: "hidden", marginBottom: 24, border: `1px solid ${COLORS.border}` }}>
            {["login", "register"].map(m => (
              <button key={m} onClick={() => setMode(m)} style={{ flex: 1, padding: "10px", background: mode === m ? "linear-gradient(135deg, #1565C0, #00897B)" : "transparent", color: mode === m ? "#fff" : COLORS.whiteDim, border: "none", cursor: "pointer", fontSize: 12, fontWeight: 700, letterSpacing: "0.1em", textTransform: "uppercase", fontFamily: "monospace" }}>
                {m}
              </button>
            ))}
          </div>

          {mode === "register" && (
            <div style={{ marginBottom: 16 }}>
              <label style={{ color: COLORS.whiteDim, fontSize: 11, letterSpacing: "0.1em" }}>COMPANY NAME</label>
              <input value={form.company} onChange={e => setForm({ ...form, company: e.target.value })} placeholder="e.g. VYJ Groups" style={{ width: "100%", padding: "10px 12px", background: COLORS.navyMid, border: `1px solid ${COLORS.border}`, borderRadius: 8, color: COLORS.white, fontSize: 13, marginTop: 6, outline: "none", boxSizing: "border-box" }} />
            </div>
          )}

          <div style={{ marginBottom: 16 }}>
            <label style={{ color: COLORS.whiteDim, fontSize: 11, letterSpacing: "0.1em" }}>EMAIL ADDRESS</label>
            <input value={form.email} onChange={e => setForm({ ...form, email: e.target.value })} placeholder="admin@vyjgroups.com" type="email" style={{ width: "100%", padding: "10px 12px", background: COLORS.navyMid, border: `1px solid ${COLORS.border}`, borderRadius: 8, color: COLORS.white, fontSize: 13, marginTop: 6, outline: "none", boxSizing: "border-box" }} />
          </div>

          <div style={{ marginBottom: 24 }}>
            <label style={{ color: COLORS.whiteDim, fontSize: 11, letterSpacing: "0.1em" }}>PASSWORD</label>
            <input value={form.password} onChange={e => setForm({ ...form, password: e.target.value })} placeholder="••••••••" type="password" style={{ width: "100%", padding: "10px 12px", background: COLORS.navyMid, border: `1px solid ${COLORS.border}`, borderRadius: 8, color: COLORS.white, fontSize: 13, marginTop: 6, outline: "none", boxSizing: "border-box" }} />
          </div>

          <Button onClick={handleSubmit} disabled={loading} style={{ width: "100%", padding: "13px", fontSize: 14, justifyContent: "center" }}>
            {loading ? "⟳ Authenticating..." : mode === "login" ? "→ Sign In to Platform" : "→ Create Account"}
          </Button>

          <div style={{ textAlign: "center", marginTop: 16, color: COLORS.whiteDim, fontSize: 11 }}>
            Demo: use any email & password
          </div>
        </Card>

        <div style={{ display: "flex", justifyContent: "center", gap: 20, marginTop: 24 }}>
          {["🔒 SOC2 Compliant", "🌐 WCO Standards", "⚡ 99.9% Uptime"].map(t => (
            <span key={t} style={{ color: COLORS.whiteDim, fontSize: 10, letterSpacing: "0.08em" }}>{t}</span>
          ))}
        </div>
      </div>
    </div>
  );
}

// ============================================================
// SIDEBAR NAV
// ============================================================
const NAV_ITEMS = [
  { id: "dashboard", icon: "⬡", label: "Dashboard" },
  { id: "upload", icon: "⬆", label: "Upload Documents" },
  { id: "hscode", icon: "◈", label: "HS Classification" },
  { id: "compliance", icon: "◉", label: "Compliance Engine" },
  { id: "documents", icon: "▦", label: "Document Preview" },
  { id: "tracking", icon: "◎", label: "Submission Tracking" },
];

function Sidebar({ active, onNav, user }) {
  return (
    <div style={{ width: 230, minHeight: "100vh", background: COLORS.navyMid, borderRight: `1px solid ${COLORS.border}`, display: "flex", flexDirection: "column", position: "fixed", left: 0, top: 0, bottom: 0, zIndex: 100 }}>
      <div style={{ padding: "20px 16px", borderBottom: `1px solid ${COLORS.border}` }}>
        <VYJLogo size={32} />
      </div>

      <div style={{ padding: "8px", flex: 1 }}>
        {NAV_ITEMS.map(item => (
          <button key={item.id} onClick={() => onNav(item.id)} style={{
            width: "100%", padding: "11px 14px", borderRadius: 8, marginBottom: 2,
            background: active === item.id ? "linear-gradient(135deg, rgba(21,101,192,0.4), rgba(0,137,123,0.3))" : "transparent",
            border: active === item.id ? `1px solid ${COLORS.border}` : "1px solid transparent",
            color: active === item.id ? COLORS.white : COLORS.whiteDim,
            cursor: "pointer", display: "flex", alignItems: "center", gap: 10,
            fontSize: 13, fontFamily: "monospace", fontWeight: active === item.id ? 700 : 400,
            transition: "all 0.15s", textAlign: "left",
            boxShadow: active === item.id ? "0 0 12px rgba(0,188,212,0.1)" : "none"
          }}>
            <span style={{ fontSize: 16, opacity: active === item.id ? 1 : 0.5 }}>{item.icon}</span>
            {item.label}
          </button>
        ))}
      </div>

      <div style={{ padding: "16px", borderTop: `1px solid ${COLORS.border}` }}>
        <div style={{ display: "flex", alignItems: "center", gap: 10 }}>
          <div style={{ width: 34, height: 34, borderRadius: 8, background: "linear-gradient(135deg, #1565C0, #00897B)", display: "flex", alignItems: "center", justifyContent: "center", fontSize: 14, fontWeight: 800, color: "#fff" }}>
            {(user?.email?.[0] || "U").toUpperCase()}
          </div>
          <div>
            <div style={{ color: COLORS.white, fontSize: 12, fontWeight: 700 }}>{user?.company || "VYJ Groups"}</div>
            <div style={{ color: COLORS.whiteDim, fontSize: 10 }}>{user?.email || "admin@vyj.com"}</div>
          </div>
        </div>
      </div>
    </div>
  );
}

// ============================================================
// PAGE: DASHBOARD
// ============================================================
function DashboardPage() {
  const shipments = [
    { id: "SHP-2024-001", route: "CN → IN", status: "Cleared", hs: "8471.30", risk: "Low", date: "2024-01-15" },
    { id: "SHP-2024-002", route: "DE → US", status: "Pending", hs: "8544.42", risk: "Medium", date: "2024-01-16" },
    { id: "SHP-2024-003", route: "JP → UK", status: "Hold", hs: "9018.19", risk: "High", date: "2024-01-17" },
    { id: "SHP-2024-004", route: "US → AU", status: "Cleared", hs: "3004.90", risk: "Low", date: "2024-01-18" },
    { id: "SHP-2024-005", route: "IN → AE", status: "Processing", hs: "6203.42", risk: "Low", date: "2024-01-19" },
  ];

  const alerts = [
    { type: "danger", msg: "SHP-2024-003: Medical device requires FDA prior notice — blocked at US CBP" },
    { type: "warning", msg: "SHP-2024-002: Cable assembly may require FCC certification for US import" },
    { type: "warning", msg: "HS 3004.90 — Pharma shipment to AU: TGA pre-approval recommended" },
  ];

  return (
    <div>
      <div style={{ marginBottom: 28 }}>
        <h1 style={{ color: COLORS.white, fontSize: 22, fontWeight: 800, fontFamily: "Georgia, serif", margin: 0 }}>Operations Dashboard</h1>
        <p style={{ color: COLORS.whiteDim, fontSize: 13, margin: "6px 0 0", fontFamily: "monospace" }}>Real-time overview · {new Date().toLocaleDateString("en-GB", { weekday: "long", year: "numeric", month: "long", day: "numeric" })}</p>
      </div>

      <div style={{ display: "flex", gap: 16, marginBottom: 24, flexWrap: "wrap" }}>
        <StatCard icon="📦" label="TOTAL SHIPMENTS" value="1,247" delta="+12% this month" color={COLORS.blueAccent} />
        <StatCard icon="✅" label="CLEARED TODAY" value="38" delta="+5 vs yesterday" color={COLORS.success} />
        <StatCard icon="⚠️" label="COMPLIANCE ALERTS" value="7" color={COLORS.warning} />
        <StatCard icon="⏱" label="AVG CLEARANCE TIME" value="4.2h" delta="-38% vs manual" color={COLORS.greenGlow} />
        <StatCard icon="🎯" label="AI ACCURACY" value="97.4%" delta="+1.2% this quarter" color={COLORS.blueAccent} />
      </div>

      {/* Alerts */}
      <Card style={{ marginBottom: 24 }}>
        <div style={{ color: COLORS.white, fontSize: 14, fontWeight: 700, marginBottom: 14, display: "flex", alignItems: "center", gap: 8 }}>
          <span>⚡</span> Active Compliance Alerts
        </div>
        {alerts.map((a, i) => (
          <div key={i} style={{ display: "flex", alignItems: "flex-start", gap: 10, padding: "10px 12px", borderRadius: 8, marginBottom: 8, background: a.type === "danger" ? "rgba(239,83,80,0.08)" : "rgba(255,167,38,0.08)", border: `1px solid ${a.type === "danger" ? "rgba(239,83,80,0.25)" : "rgba(255,167,38,0.25)"}` }}>
            <span style={{ fontSize: 14 }}>{a.type === "danger" ? "🔴" : "🟡"}</span>
            <span style={{ color: COLORS.whiteDim, fontSize: 12, fontFamily: "monospace" }}>{a.msg}</span>
          </div>
        ))}
      </Card>

      {/* Recent Shipments Table */}
      <Card>
        <div style={{ color: COLORS.white, fontSize: 14, fontWeight: 700, marginBottom: 16 }}>Recent Shipments</div>
        <div style={{ overflowX: "auto" }}>
          <table style={{ width: "100%", borderCollapse: "collapse", fontFamily: "monospace", fontSize: 12 }}>
            <thead>
              <tr style={{ borderBottom: `1px solid ${COLORS.border}` }}>
                {["Shipment ID", "Route", "HS Code", "Risk Level", "Status", "Date"].map(h => (
                  <th key={h} style={{ color: COLORS.whiteDim, padding: "8px 12px", textAlign: "left", letterSpacing: "0.08em", fontSize: 10, fontWeight: 700 }}>{h}</th>
                ))}
              </tr>
            </thead>
            <tbody>
              {shipments.map((s, i) => (
                <tr key={i} style={{ borderBottom: `1px solid ${COLORS.border}22` }}>
                  <td style={{ padding: "10px 12px", color: COLORS.blueAccent }}>{s.id}</td>
                  <td style={{ padding: "10px 12px", color: COLORS.white }}>{s.route}</td>
                  <td style={{ padding: "10px 12px", color: COLORS.greenGlow }}>{s.hs}</td>
                  <td style={{ padding: "10px 12px" }}>
                    <Badge color={s.risk === "High" ? "danger" : s.risk === "Medium" ? "warning" : "success"}>{s.risk}</Badge>
                  </td>
                  <td style={{ padding: "10px 12px" }}>
                    <Badge color={s.status === "Cleared" ? "success" : s.status === "Hold" ? "danger" : "blue"}>{s.status}</Badge>
                  </td>
                  <td style={{ padding: "10px 12px", color: COLORS.whiteDim }}>{s.date}</td>
                </tr>
              ))}
            </tbody>
          </table>
        </div>
      </Card>
    </div>
  );
}

// ============================================================
// PAGE: DOCUMENT UPLOAD
// ============================================================
function UploadPage({ onResult }) {
  const [files, setFiles] = useState([]);
  const [loading, setLoading] = useState(false);
  const [result, setResult] = useState(null);
  const [drag, setDrag] = useState(false);
  const fileRef = useRef();

  const handleFiles = (f) => {
    const arr = Array.from(f);
    setFiles(arr);
    setResult(null);
  };

  const analyzeWithAI = async () => {
    if (!files.length) return;
    setLoading(true);
    try {
      const fileName = files[0].name;
      const prompt = `A user uploaded a customs document named "${fileName}". 
      Simulate extraction of fields from this document and return JSON with:
      {
        "productDescription": "detailed product description",
        "quantity": "number with unit",
        "weight": "weight in kg",
        "value": "USD value",
        "incoterms": "one of EXW/FOB/CIF/DDP/DAP",
        "originCountry": "country name",
        "destinationCountry": "country name",
        "hsCodeSuggestion": "6-digit HS code",
        "confidence": number between 0.85 and 0.99,
        "extractedFields": 7
      }
      Make the data realistic for international trade.`;

      const data = await callClaudeAI(prompt);
      setResult(data);
      if (onResult) onResult(data);
    } catch (e) {
      setResult({ error: "AI parsing failed. Please retry." });
    }
    setLoading(false);
  };

  return (
    <div>
      <div style={{ marginBottom: 28 }}>
        <h1 style={{ color: COLORS.white, fontSize: 22, fontWeight: 800, fontFamily: "Georgia, serif", margin: 0 }}>Document Upload & AI Parsing</h1>
        <p style={{ color: COLORS.whiteDim, fontSize: 13, margin: "6px 0 0", fontFamily: "monospace" }}>Upload commercial invoices, packing lists, or certificates of origin</p>
      </div>

      <div style={{ display: "grid", gridTemplateColumns: "1fr 1fr", gap: 20 }}>
        {/* Upload Zone */}
        <Card>
          <div
            onDragOver={e => { e.preventDefault(); setDrag(true); }}
            onDragLeave={() => setDrag(false)}
            onDrop={e => { e.preventDefault(); setDrag(false); handleFiles(e.dataTransfer.files); }}
            onClick={() => fileRef.current.click()}
            style={{
              border: `2px dashed ${drag ? COLORS.greenGlow : COLORS.border}`,
              borderRadius: 10, padding: "48px 24px", textAlign: "center", cursor: "pointer",
              background: drag ? "rgba(0,188,212,0.05)" : "transparent",
              transition: "all 0.2s"
            }}
          >
            <div style={{ fontSize: 48, marginBottom: 12 }}>📄</div>
            <div style={{ color: COLORS.white, fontWeight: 700, fontSize: 15, marginBottom: 6 }}>Drop files here or click to browse</div>
            <div style={{ color: COLORS.whiteDim, fontSize: 12 }}>Supports PDF, XLSX, PNG, JPG · Max 50MB</div>
            <input ref={fileRef} type="file" multiple accept=".pdf,.xlsx,.xls,.png,.jpg,.jpeg" onChange={e => handleFiles(e.target.files)} style={{ display: "none" }} />
          </div>

          {files.length > 0 && (
            <div style={{ marginTop: 16 }}>
              {files.map((f, i) => (
                <div key={i} style={{ display: "flex", alignItems: "center", gap: 10, padding: "8px 12px", background: COLORS.navyMid, borderRadius: 8, marginBottom: 6 }}>
                  <span>📎</span>
                  <span style={{ color: COLORS.white, fontSize: 12, flex: 1 }}>{f.name}</span>
                  <span style={{ color: COLORS.whiteDim, fontSize: 11 }}>{(f.size / 1024).toFixed(0)} KB</span>
                </div>
              ))}
              <Button onClick={analyzeWithAI} disabled={loading} style={{ width: "100%", marginTop: 12 }}>
                {loading ? "⟳ AI Analyzing Document..." : "⚡ Analyze with AI"}
              </Button>
            </div>
          )}
        </Card>

        {/* Result Panel */}
        <Card glow={!!result}>
          <div style={{ color: COLORS.whiteDim, fontSize: 11, letterSpacing: "0.1em", marginBottom: 14 }}>AI EXTRACTION RESULTS</div>
          {loading && <Spinner />}
          {!loading && !result && (
            <div style={{ textAlign: "center", padding: "40px 0", color: COLORS.whiteDim, fontSize: 13 }}>
              <div style={{ fontSize: 36, marginBottom: 10 }}>🤖</div>
              Upload a document to begin AI analysis
            </div>
          )}
          {result && !result.error && (
            <div>
              <div style={{ display: "flex", justifyContent: "space-between", alignItems: "center", marginBottom: 14 }}>
                <span style={{ color: COLORS.success, fontSize: 12, fontFamily: "monospace" }}>✓ Extraction Complete</span>
                <Badge color="green">{result.confidence ? `${(result.confidence * 100).toFixed(1)}% Confidence` : "High Confidence"}</Badge>
              </div>
              {Object.entries(result).filter(([k]) => k !== "confidence" && k !== "extractedFields").map(([k, v]) => (
                <div key={k} style={{ display: "flex", borderBottom: `1px solid ${COLORS.border}22`, padding: "8px 0" }}>
                  <span style={{ color: COLORS.whiteDim, fontSize: 11, width: 160, flexShrink: 0, fontFamily: "monospace", textTransform: "uppercase", letterSpacing: "0.06em" }}>{k.replace(/([A-Z])/g, " $1")}</span>
                  <span style={{ color: COLORS.white, fontSize: 12 }}>{String(v)}</span>
                </div>
              ))}
            </div>
          )}
          {result?.error && <div style={{ color: COLORS.danger, fontSize: 13 }}>{result.error}</div>}
        </Card>
      </div>

      {/* Supported Formats */}
      <Card style={{ marginTop: 20 }}>
        <div style={{ display: "flex", gap: 20, flexWrap: "wrap" }}>
          {[["📄 Commercial Invoice", "Auto-extract line items, values, parties"],
            ["📦 Packing List", "Quantities, weights, dimensions per SKU"],
            ["🏭 Bill of Lading", "Carrier, vessel, port, freight details"],
            ["📜 Certificate of Origin", "Origin declarations, preferential duty"]].map(([t, d]) => (
            <div key={t} style={{ flex: 1, minWidth: 180 }}>
              <div style={{ color: COLORS.white, fontSize: 13, fontWeight: 700, marginBottom: 4 }}>{t}</div>
              <div style={{ color: COLORS.whiteDim, fontSize: 11 }}>{d}</div>
            </div>
          ))}
        </div>
      </Card>
    </div>
  );
}

// ============================================================
// PAGE: HS CODE CLASSIFICATION
// ============================================================
function HSCodePage() {
  const [input, setInput] = useState("");
  const [loading, setLoading] = useState(false);
  const [result, setResult] = useState(null);

  const classify = async () => {
    if (!input.trim()) return;
    setLoading(true);
    try {
      const prompt = `Classify this product for customs/HS code: "${input}"
      Return JSON:
      {
        "predictedHSCode": "XXXX.XX",
        "description": "official WCO description of this HS heading",
        "confidence": number 0.0-1.0,
        "confidenceLabel": "High/Medium/Low",
        "reasoning": "2-3 sentence explanation of why this HS code was chosen, mentioning product function, material, and classification rules",
        "alternatives": [
          {"code": "XXXX.XX", "description": "heading description", "confidence": number, "reason": "why this could apply"},
          {"code": "XXXX.XX", "description": "heading description", "confidence": number, "reason": "why this could apply"},
          {"code": "XXXX.XX", "description": "heading description", "confidence": number, "reason": "why this could apply"}
        ],
        "requiredAttributes": ["material composition", "function", "end use"],
        "dutyNotes": "brief note on typical duty treatment globally"
      }`;
      const data = await callClaudeAI(prompt);
      setResult(data);
    } catch (e) {
      setResult({ error: "Classification failed. Retry." });
    }
    setLoading(false);
  };

  return (
    <div>
      <div style={{ marginBottom: 28 }}>
        <h1 style={{ color: COLORS.white, fontSize: 22, fontWeight: 800, fontFamily: "Georgia, serif", margin: 0 }}>AI HS Code Classification</h1>
        <p style={{ color: COLORS.whiteDim, fontSize: 13, margin: "6px 0 0", fontFamily: "monospace" }}>Powered by Large Language Models · WCO Harmonized System 2022</p>
      </div>

      <Card style={{ marginBottom: 20 }}>
        <label style={{ color: COLORS.whiteDim, fontSize: 11, letterSpacing: "0.1em" }}>PRODUCT DESCRIPTION</label>
        <textarea
          value={input}
          onChange={e => setInput(e.target.value)}
          placeholder="e.g. Lithium-ion rechargeable battery pack, 48V 20Ah, for electric bicycles, with integrated BMS..."
          rows={4}
          style={{ width: "100%", padding: "12px", background: COLORS.navyMid, border: `1px solid ${COLORS.border}`, borderRadius: 8, color: COLORS.white, fontSize: 13, marginTop: 8, outline: "none", resize: "vertical", fontFamily: "monospace", boxSizing: "border-box" }}
        />
        <div style={{ display: "flex", gap: 10, marginTop: 12, flexWrap: "wrap" }}>
          <Button onClick={classify} disabled={loading || !input.trim()}>
            {loading ? "⟳ Classifying..." : "🔍 Classify with AI"}
          </Button>
          {["Laptop computer", "Cotton T-shirt", "Solar panel 400W", "Pharmaceutical tablets"].map(ex => (
            <button key={ex} onClick={() => setInput(ex)} style={{ padding: "8px 14px", background: "transparent", border: `1px solid ${COLORS.border}`, borderRadius: 6, color: COLORS.whiteDim, cursor: "pointer", fontSize: 11, fontFamily: "monospace" }}>{ex}</button>
          ))}
        </div>
      </Card>

      {loading && <Card><Spinner /></Card>}

      {result && !result.error && (
        <div style={{ display: "grid", gridTemplateColumns: "1fr 1fr", gap: 20 }}>
          {/* Primary Result */}
          <Card glow>
            <div style={{ color: COLORS.whiteDim, fontSize: 10, letterSpacing: "0.12em", marginBottom: 12 }}>PRIMARY CLASSIFICATION</div>
            <div style={{ display: "flex", alignItems: "baseline", gap: 12, marginBottom: 8 }}>
              <div style={{ fontSize: 36, fontWeight: 800, fontFamily: "Georgia, serif", background: "linear-gradient(135deg, #00BCD4, #1976D2)", WebkitBackgroundClip: "text", WebkitTextFillColor: "transparent" }}>
                {result.predictedHSCode}
              </div>
              <Badge color={result.confidenceLabel === "High" ? "green" : result.confidenceLabel === "Medium" ? "warning" : "danger"}>
                {result.confidenceLabel} · {result.confidence ? `${(result.confidence * 100).toFixed(1)}%` : ""}
              </Badge>
            </div>
            <div style={{ color: COLORS.white, fontSize: 13, marginBottom: 12 }}>{result.description}</div>

            {/* Confidence Bar */}
            <div style={{ marginBottom: 16 }}>
              <div style={{ display: "flex", justifyContent: "space-between", marginBottom: 4 }}>
                <span style={{ color: COLORS.whiteDim, fontSize: 10 }}>AI Confidence</span>
                <span style={{ color: COLORS.greenGlow, fontSize: 10 }}>{result.confidence ? `${(result.confidence * 100).toFixed(1)}%` : "N/A"}</span>
              </div>
              <div style={{ height: 6, background: COLORS.border, borderRadius: 3 }}>
                <div style={{ height: "100%", width: `${(result.confidence || 0.9) * 100}%`, background: "linear-gradient(90deg, #1976D2, #00BCD4)", borderRadius: 3 }} />
              </div>
            </div>

            <div style={{ background: COLORS.navyMid, borderRadius: 8, padding: "12px", borderLeft: `3px solid ${COLORS.greenGlow}` }}>
              <div style={{ color: COLORS.greenGlow, fontSize: 10, letterSpacing: "0.1em", marginBottom: 6 }}>AI REASONING</div>
              <div style={{ color: COLORS.whiteDim, fontSize: 12, lineHeight: 1.6 }}>{result.reasoning}</div>
            </div>

            {result.requiredAttributes && (
              <div style={{ marginTop: 14 }}>
                <div style={{ color: COLORS.whiteDim, fontSize: 10, letterSpacing: "0.1em", marginBottom: 8 }}>REQUIRED ATTRIBUTES FOR DECLARATION</div>
                <div style={{ display: "flex", flexWrap: "wrap", gap: 6 }}>
                  {result.requiredAttributes.map((a, i) => <Badge key={i} color="blue">{a}</Badge>)}
                </div>
              </div>
            )}
          </Card>

          {/* Alternatives */}
          <div>
            <Card>
              <div style={{ color: COLORS.whiteDim, fontSize: 10, letterSpacing: "0.12em", marginBottom: 12 }}>TOP 3 ALTERNATIVES</div>
              {(result.alternatives || []).map((alt, i) => (
                <div key={i} style={{ padding: "12px", background: COLORS.navyMid, borderRadius: 8, marginBottom: 8, border: `1px solid ${COLORS.border}` }}>
                  <div style={{ display: "flex", justifyContent: "space-between", alignItems: "center", marginBottom: 6 }}>
                    <span style={{ color: COLORS.blueAccent, fontWeight: 800, fontFamily: "Georgia, serif", fontSize: 15 }}>#{i + 1} · {alt.code}</span>
                    <Badge color="blue">{alt.confidence ? `${(alt.confidence * 100).toFixed(0)}%` : "—"}</Badge>
                  </div>
                  <div style={{ color: COLORS.white, fontSize: 12, marginBottom: 4 }}>{alt.description}</div>
                  <div style={{ color: COLORS.whiteDim, fontSize: 11 }}>{alt.reason}</div>
                </div>
              ))}
            </Card>

            {result.dutyNotes && (
              <Card style={{ marginTop: 16 }}>
                <div style={{ color: COLORS.whiteDim, fontSize: 10, letterSpacing: "0.1em", marginBottom: 8 }}>DUTY GUIDANCE</div>
                <div style={{ color: COLORS.white, fontSize: 12, lineHeight: 1.6 }}>{result.dutyNotes}</div>
              </Card>
            )}
          </div>
        </div>
      )}
    </div>
  );
}

// ============================================================
// PAGE: COMPLIANCE ENGINE
// ============================================================
function CompliancePage() {
  const [form, setForm] = useState({ hs: "", origin: "", destination: "", value: "", product: "" });
  const [loading, setLoading] = useState(false);
  const [result, setResult] = useState(null);

  const check = async () => {
    setLoading(true);
    try {
      const prompt = `Perform customs compliance check:
      HS Code: ${form.hs}, Origin: ${form.origin}, Destination: ${form.destination}, Value: USD ${form.value}, Product: ${form.product}
      
      Return JSON:
      {
        "overallRisk": "Low/Medium/High/Critical",
        "riskScore": number 0-100,
        "issues": [
          {"type": "Sanctions/License/Restriction/Duty/Documentation", "severity": "Low/Medium/High", "description": "specific issue", "action": "recommended action"}
        ],
        "sanctions": {"applies": boolean, "details": "details or none"},
        "licenseRequired": boolean,
        "licenseType": "type or null",
        "dutyEstimate": "estimated duty rate or range",
        "vatNote": "VAT/GST note for destination",
        "recommendations": ["actionable recommendation 1", "recommendation 2", "recommendation 3"]
      }`;
      const data = await callClaudeAI(prompt);
      setResult(data);
    } catch (e) {
      setResult({ error: "Check failed." });
    }
    setLoading(false);
  };

  const riskColor = { Low: "success", Medium: "warning", High: "danger", Critical: "danger" };

  return (
    <div>
      <div style={{ marginBottom: 28 }}>
        <h1 style={{ color: COLORS.white, fontSize: 22, fontWeight: 800, fontFamily: "Georgia, serif", margin: 0 }}>AI Compliance Engine</h1>
        <p style={{ color: COLORS.whiteDim, fontSize: 13, margin: "6px 0 0", fontFamily: "monospace" }}>Sanctions screening · License checks · Country restrictions · Value thresholds</p>
      </div>

      <div style={{ display: "grid", gridTemplateColumns: "340px 1fr", gap: 20 }}>
        <Card>
          <div style={{ color: COLORS.whiteDim, fontSize: 11, letterSpacing: "0.1em", marginBottom: 16 }}>SHIPMENT PARAMETERS</div>
          {[
            { key: "product", label: "PRODUCT DESCRIPTION", ph: "e.g. Drone with camera, 2kg" },
            { key: "hs", label: "HS CODE", ph: "e.g. 8806.21" },
            { key: "origin", label: "ORIGIN COUNTRY", ph: "e.g. China" },
            { key: "destination", label: "DESTINATION COUNTRY", ph: "e.g. United States" },
            { key: "value", label: "SHIPMENT VALUE (USD)", ph: "e.g. 15000" },
          ].map(f => (
            <div key={f.key} style={{ marginBottom: 14 }}>
              <label style={{ color: COLORS.whiteDim, fontSize: 10, letterSpacing: "0.1em" }}>{f.label}</label>
              <input value={form[f.key]} onChange={e => setForm({ ...form, [f.key]: e.target.value })} placeholder={f.ph}
                style={{ width: "100%", padding: "9px 11px", background: COLORS.navyMid, border: `1px solid ${COLORS.border}`, borderRadius: 7, color: COLORS.white, fontSize: 12, marginTop: 5, outline: "none", boxSizing: "border-box", fontFamily: "monospace" }} />
            </div>
          ))}
          <Button onClick={check} disabled={loading || !form.product} style={{ width: "100%" }}>
            {loading ? "⟳ AI Screening..." : "🔍 Run Compliance Check"}
          </Button>
        </Card>

        <div>
          {loading && <Card><Spinner /></Card>}
          {result && !result.error && (
            <div style={{ display: "flex", flexDirection: "column", gap: 16 }}>
              {/* Risk Score */}
              <Card glow={result.overallRisk === "High" || result.overallRisk === "Critical"}>
                <div style={{ display: "flex", alignItems: "center", justifyContent: "space-between", marginBottom: 16 }}>
                  <div>
                    <div style={{ color: COLORS.whiteDim, fontSize: 10, letterSpacing: "0.12em" }}>OVERALL RISK ASSESSMENT</div>
                    <div style={{ fontSize: 32, fontWeight: 800, fontFamily: "Georgia, serif", color: result.overallRisk === "Low" ? COLORS.success : result.overallRisk === "Medium" ? COLORS.warning : COLORS.danger, marginTop: 4 }}>
                      {result.overallRisk}
                    </div>
                  </div>
                  <div style={{ width: 70, height: 70, borderRadius: "50%", background: `conic-gradient(${result.overallRisk === "Low" ? COLORS.success : result.overallRisk === "Medium" ? COLORS.warning : COLORS.danger} ${(result.riskScore || 50) * 3.6}deg, ${COLORS.border} 0)`, display: "flex", alignItems: "center", justifyContent: "center" }}>
                    <div style={{ width: 54, height: 54, borderRadius: "50%", background: COLORS.card, display: "flex", alignItems: "center", justifyContent: "center", color: COLORS.white, fontSize: 14, fontWeight: 800 }}>
                      {result.riskScore}
                    </div>
                  </div>
                </div>
                <div style={{ display: "flex", gap: 12, flexWrap: "wrap" }}>
                  <div style={{ padding: "6px 12px", background: result.sanctions?.applies ? "rgba(239,83,80,0.15)" : "rgba(102,187,106,0.1)", borderRadius: 6, border: `1px solid ${result.sanctions?.applies ? "rgba(239,83,80,0.3)" : "rgba(102,187,106,0.2)"}` }}>
                    <span style={{ color: result.sanctions?.applies ? COLORS.danger : COLORS.success, fontSize: 11, fontFamily: "monospace" }}>{result.sanctions?.applies ? "⛔ SANCTIONS APPLY" : "✓ NO SANCTIONS"}</span>
                  </div>
                  <div style={{ padding: "6px 12px", background: result.licenseRequired ? "rgba(255,167,38,0.15)" : "rgba(102,187,106,0.1)", borderRadius: 6, border: `1px solid ${result.licenseRequired ? "rgba(255,167,38,0.3)" : "rgba(102,187,106,0.2)"}` }}>
                    <span style={{ color: result.licenseRequired ? COLORS.warning : COLORS.success, fontSize: 11, fontFamily: "monospace" }}>{result.licenseRequired ? `⚠ LICENSE: ${result.licenseType || "Required"}` : "✓ NO LICENSE NEEDED"}</span>
                  </div>
                </div>
              </Card>

              {/* Issues */}
              {result.issues?.length > 0 && (
                <Card>
                  <div style={{ color: COLORS.whiteDim, fontSize: 10, letterSpacing: "0.12em", marginBottom: 12 }}>COMPLIANCE ISSUES DETECTED</div>
                  {result.issues.map((issue, i) => (
                    <div key={i} style={{ padding: "12px", borderRadius: 8, marginBottom: 8, background: COLORS.navyMid, borderLeft: `3px solid ${issue.severity === "High" ? COLORS.danger : issue.severity === "Medium" ? COLORS.warning : COLORS.success}` }}>
                      <div style={{ display: "flex", justifyContent: "space-between", marginBottom: 4 }}>
                        <span style={{ color: COLORS.white, fontSize: 12, fontWeight: 700 }}>{issue.type}</span>
                        <Badge color={issue.severity === "High" ? "danger" : issue.severity === "Medium" ? "warning" : "success"}>{issue.severity}</Badge>
                      </div>
                      <div style={{ color: COLORS.whiteDim, fontSize: 12, marginBottom: 6 }}>{issue.description}</div>
                      <div style={{ color: COLORS.blueAccent, fontSize: 11, fontFamily: "monospace" }}>→ {issue.action}</div>
                    </div>
                  ))}
                </Card>
              )}

              {/* Duty & Recommendations */}
              <div style={{ display: "grid", gridTemplateColumns: "1fr 1fr", gap: 16 }}>
                <Card>
                  <div style={{ color: COLORS.whiteDim, fontSize: 10, letterSpacing: "0.1em", marginBottom: 8 }}>DUTY ESTIMATE</div>
                  <div style={{ color: COLORS.greenGlow, fontSize: 20, fontWeight: 800 }}>{result.dutyEstimate}</div>
                  {result.vatNote && <div style={{ color: COLORS.whiteDim, fontSize: 12, marginTop: 8 }}>{result.vatNote}</div>}
                </Card>
                <Card>
                  <div style={{ color: COLORS.whiteDim, fontSize: 10, letterSpacing: "0.1em", marginBottom: 8 }}>AI RECOMMENDATIONS</div>
                  {(result.recommendations || []).map((r, i) => (
                    <div key={i} style={{ color: COLORS.whiteDim, fontSize: 12, marginBottom: 6, paddingLeft: 12, borderLeft: `2px solid ${COLORS.blueAccent}` }}>{r}</div>
                  ))}
                </Card>
              </div>
            </div>
          )}
          {!loading && !result && (
            <Card style={{ textAlign: "center", padding: "60px" }}>
              <div style={{ fontSize: 48, marginBottom: 12 }}>🛡️</div>
              <div style={{ color: COLORS.whiteDim, fontSize: 14 }}>Fill in shipment details to run AI compliance screening</div>
            </Card>
          )}
        </div>
      </div>
    </div>
  );
}

// ============================================================
// PAGE: DOCUMENT PREVIEW
// ============================================================
function DocumentPage() {
  const [loading, setLoading] = useState(false);
  const [doc, setDoc] = useState(null);
  const [form, setForm] = useState({ exporter: "VYJ Groups Pvt Ltd", importer: "", origin: "India", destination: "Germany", product: "", hs: "", value: "" });

  const generate = async () => {
    setLoading(true);
    try {
      const prompt = `Generate a complete customs declaration document as JSON:
      Exporter: ${form.exporter}, Importer: ${form.importer || "ABC GmbH"}, 
      Origin: ${form.origin}, Destination: ${form.destination},
      Product: ${form.product || "Electronic Components"}, HS: ${form.hs || "8541.10"}, Value: USD ${form.value || "5000"}
      
      Return JSON:
      {
        "declarationNumber": "CUSDDEC-XXXX-XXXXX",
        "declarationDate": "ISO date",
        "exporter": {"name": string, "address": string, "country": string, "eori": string},
        "importer": {"name": string, "address": string, "country": string, "vatNumber": string},
        "transport": {"mode": "Air/Sea/Road/Rail", "vessel": string, "port": string},
        "lineItems": [
          {"description": string, "hsCode": string, "quantity": string, "weight": string, "value": string, "origin": string, "dutyRate": string}
        ],
        "totalValue": string,
        "totalWeight": string,
        "incoterms": string,
        "currency": "USD",
        "specialInstructions": string
      }`;
      const data = await callClaudeAI(prompt);
      setDoc(data);
    } catch (e) { setDoc({ error: true }); }
    setLoading(false);
  };

  return (
    <div>
      <div style={{ marginBottom: 28 }}>
        <h1 style={{ color: COLORS.white, fontSize: 22, fontWeight: 800, fontFamily: "Georgia, serif", margin: 0 }}>Auto-Generated Customs Declaration</h1>
        <p style={{ color: COLORS.whiteDim, fontSize: 13, margin: "6px 0 0", fontFamily: "monospace" }}>AI generates complete, validated customs documents instantly</p>
      </div>

      <div style={{ display: "grid", gridTemplateColumns: "300px 1fr", gap: 20 }}>
        <Card>
          <div style={{ color: COLORS.whiteDim, fontSize: 11, letterSpacing: "0.1em", marginBottom: 14 }}>SHIPMENT DETAILS</div>
          {[
            ["exporter", "EXPORTER NAME"],
            ["importer", "IMPORTER NAME"],
            ["origin", "ORIGIN COUNTRY"],
            ["destination", "DESTINATION COUNTRY"],
            ["product", "PRODUCT DESCRIPTION"],
            ["hs", "HS CODE"],
            ["value", "TOTAL VALUE (USD)"],
          ].map(([k, l]) => (
            <div key={k} style={{ marginBottom: 12 }}>
              <label style={{ color: COLORS.whiteDim, fontSize: 10, letterSpacing: "0.08em" }}>{l}</label>
              <input value={form[k]} onChange={e => setForm({ ...form, [k]: e.target.value })}
                style={{ width: "100%", padding: "8px 10px", background: COLORS.navyMid, border: `1px solid ${COLORS.border}`, borderRadius: 6, color: COLORS.white, fontSize: 12, marginTop: 4, outline: "none", boxSizing: "border-box", fontFamily: "monospace" }} />
            </div>
          ))}
          <Button onClick={generate} disabled={loading} style={{ width: "100%" }}>
            {loading ? "⟳ Generating..." : "📄 Generate Document"}
          </Button>
        </Card>

        <div>
          {loading && <Card><Spinner /></Card>}
          {doc && !doc.error && (
            <Card glow>
              {/* Document Header */}
              <div style={{ borderBottom: `2px solid ${COLORS.border}`, paddingBottom: 16, marginBottom: 16, display: "flex", justifyContent: "space-between", alignItems: "center" }}>
                <div>
                  <div style={{ color: COLORS.white, fontSize: 16, fontWeight: 800, fontFamily: "Georgia, serif" }}>CUSTOMS DECLARATION</div>
                  <div style={{ color: COLORS.greenGlow, fontSize: 12, fontFamily: "monospace" }}>{doc.declarationNumber}</div>
                  <div style={{ color: COLORS.whiteDim, fontSize: 11 }}>{doc.declarationDate}</div>
                </div>
                <div style={{ textAlign: "right" }}>
                  <VYJLogo size={28} showText={false} />
                  <div style={{ color: COLORS.whiteDim, fontSize: 10, marginTop: 4 }}>AI-GENERATED DOCUMENT</div>
                </div>
              </div>

              <div style={{ display: "grid", gridTemplateColumns: "1fr 1fr", gap: 16, marginBottom: 16 }}>
                {[["EXPORTER", doc.exporter], ["IMPORTER", doc.importer]].map(([label, party]) => (
                  <div key={label} style={{ background: COLORS.navyMid, borderRadius: 8, padding: "12px" }}>
                    <div style={{ color: COLORS.whiteDim, fontSize: 10, letterSpacing: "0.1em", marginBottom: 8 }}>{label}</div>
                    {party && Object.entries(party).map(([k, v]) => (
                      <div key={k} style={{ color: COLORS.white, fontSize: 11, marginBottom: 2 }}><span style={{ color: COLORS.whiteDim }}>{k}: </span>{v}</div>
                    ))}
                  </div>
                ))}
              </div>

              {doc.transport && (
                <div style={{ background: COLORS.navyMid, borderRadius: 8, padding: "12px", marginBottom: 16, display: "flex", gap: 20 }}>
                  <div><span style={{ color: COLORS.whiteDim, fontSize: 10 }}>MODE: </span><span style={{ color: COLORS.white, fontSize: 12 }}>{doc.transport.mode}</span></div>
                  <div><span style={{ color: COLORS.whiteDim, fontSize: 10 }}>VESSEL: </span><span style={{ color: COLORS.white, fontSize: 12 }}>{doc.transport.vessel}</span></div>
                  <div><span style={{ color: COLORS.whiteDim, fontSize: 10 }}>PORT: </span><span style={{ color: COLORS.white, fontSize: 12 }}>{doc.transport.port}</span></div>
                  <div><span style={{ color: COLORS.whiteDim, fontSize: 10 }}>INCOTERMS: </span><span style={{ color: COLORS.greenGlow, fontSize: 12, fontWeight: 700 }}>{doc.incoterms}</span></div>
                </div>
              )}

              {/* Line Items Table */}
              <div style={{ marginBottom: 16 }}>
                <div style={{ color: COLORS.whiteDim, fontSize: 10, letterSpacing: "0.1em", marginBottom: 8 }}>LINE ITEMS</div>
                <table style={{ width: "100%", borderCollapse: "collapse", fontSize: 11, fontFamily: "monospace" }}>
                  <thead>
                    <tr style={{ background: COLORS.navyMid }}>
                      {["Description", "HS Code", "Qty", "Weight", "Value", "Origin", "Duty Rate"].map(h => (
                        <th key={h} style={{ padding: "7px 10px", color: COLORS.whiteDim, textAlign: "left", borderBottom: `1px solid ${COLORS.border}` }}>{h}</th>
                      ))}
                    </tr>
                  </thead>
                  <tbody>
                    {(doc.lineItems || []).map((item, i) => (
                      <tr key={i} style={{ borderBottom: `1px solid ${COLORS.border}22` }}>
                        <td style={{ padding: "8px 10px", color: COLORS.white }}>{item.description}</td>
                        <td style={{ padding: "8px 10px", color: COLORS.greenGlow }}>{item.hsCode}</td>
                        <td style={{ padding: "8px 10px", color: COLORS.white }}>{item.quantity}</td>
                        <td style={{ padding: "8px 10px", color: COLORS.whiteDim }}>{item.weight}</td>
                        <td style={{ padding: "8px 10px", color: COLORS.white }}>{item.value}</td>
                        <td style={{ padding: "8px 10px", color: COLORS.whiteDim }}>{item.origin}</td>
                        <td style={{ padding: "8px 10px", color: COLORS.warning }}>{item.dutyRate}</td>
                      </tr>
                    ))}
                  </tbody>
                </table>
              </div>

              <div style={{ display: "flex", gap: 16, justifyContent: "flex-end", borderTop: `1px solid ${COLORS.border}`, paddingTop: 12 }}>
                <div style={{ color: COLORS.whiteDim, fontSize: 12 }}>Total Weight: <strong style={{ color: COLORS.white }}>{doc.totalWeight}</strong></div>
                <div style={{ color: COLORS.whiteDim, fontSize: 12 }}>Total Value: <strong style={{ color: COLORS.greenGlow }}>{doc.totalValue}</strong></div>
              </div>

              {doc.specialInstructions && (
                <div style={{ marginTop: 12, padding: "8px 12px", background: "rgba(33,150,243,0.08)", borderRadius: 6, borderLeft: `2px solid ${COLORS.blueAccent}` }}>
                  <span style={{ color: COLORS.whiteDim, fontSize: 11 }}>Special Instructions: {doc.specialInstructions}</span>
                </div>
              )}

              <div style={{ display: "flex", gap: 10, marginTop: 16 }}>
                <Button variant="success">📤 Submit to Customs Portal</Button>
                <Button variant="secondary">🖨 Print Declaration</Button>
                <Button variant="secondary">📧 Send to Broker</Button>
              </div>
            </Card>
          )}
          {!loading && !doc && (
            <Card style={{ textAlign: "center", padding: "80px" }}>
              <div style={{ fontSize: 48, marginBottom: 12 }}>📋</div>
              <div style={{ color: COLORS.whiteDim, fontSize: 14 }}>Fill in details and generate your customs declaration</div>
            </Card>
          )}
        </div>
      </div>
    </div>
  );
}

// ============================================================
// PAGE: SUBMISSION TRACKING
// ============================================================
function TrackingPage() {
  const submissions = [
    { id: "SUB-2024-0891", shipment: "SHP-2024-001", lane: "CN → IN", hs: "8471.30", submitted: "2024-01-15 09:22", status: "Cleared", broker: "DHL Trade", response: "Customs Release Issued", time: "2.1h" },
    { id: "SUB-2024-0892", shipment: "SHP-2024-002", lane: "DE → US", hs: "8544.42", submitted: "2024-01-16 14:05", status: "Under Review", broker: "Flexport", response: "Awaiting CBP Review", time: "—" },
    { id: "SUB-2024-0893", shipment: "SHP-2024-003", lane: "JP → UK", hs: "9018.19", submitted: "2024-01-17 11:30", status: "Hold", broker: "Maersk Trade", response: "Additional Docs Required: FDA Notice", time: "—" },
    { id: "SUB-2024-0894", shipment: "SHP-2024-004", lane: "US → AU", hs: "3004.90", submitted: "2024-01-18 08:15", status: "Cleared", broker: "Kuehne+Nagel", response: "ABF Release Granted", time: "4.7h" },
    { id: "SUB-2024-0895", shipment: "SHP-2024-005", lane: "IN → AE", hs: "6203.42", submitted: "2024-01-19 16:40", status: "Processing", broker: "DP World Trade", response: "In Customs Queue — Priority 3", time: "—" },
    { id: "SUB-2024-0896", shipment: "SHP-2024-006", lane: "KR → BR", hs: "8703.22", submitted: "2024-01-20 10:00", status: "Cleared", broker: "Expeditors", response: "SISCOMEX Registered", time: "6.2h" },
  ];

  const statusColor = { Cleared: "success", "Under Review": "blue", Hold: "danger", Processing: "warning" };

  return (
    <div>
      <div style={{ marginBottom: 28 }}>
        <h1 style={{ color: COLORS.white, fontSize: 22, fontWeight: 800, fontFamily: "Georgia, serif", margin: 0 }}>Submission Tracking & Audit Log</h1>
        <p style={{ color: COLORS.whiteDim, fontSize: 13, margin: "6px 0 0", fontFamily: "monospace" }}>Complete audit trail · Real-time status updates · Immutable records</p>
      </div>

      <div style={{ display: "flex", gap: 16, marginBottom: 20, flexWrap: "wrap" }}>
        <StatCard icon="📤" label="TOTAL SUBMISSIONS" value="6" color={COLORS.blueAccent} />
        <StatCard icon="✅" label="CLEARED" value="3" color={COLORS.success} />
        <StatCard icon="⏳" label="IN PROGRESS" value="2" color={COLORS.warning} />
        <StatCard icon="🚫" label="ON HOLD" value="1" color={COLORS.danger} />
      </div>

      <Card>
        <div style={{ overflowX: "auto" }}>
          <table style={{ width: "100%", borderCollapse: "collapse", fontFamily: "monospace", fontSize: 11 }}>
            <thead>
              <tr style={{ borderBottom: `1px solid ${COLORS.border}` }}>
                {["Submission ID", "Shipment", "Lane", "HS Code", "Submitted", "Broker", "Status", "Response", "Clearance Time"].map(h => (
                  <th key={h} style={{ padding: "10px 12px", color: COLORS.whiteDim, textAlign: "left", fontSize: 10, letterSpacing: "0.08em", whiteSpace: "nowrap" }}>{h}</th>
                ))}
              </tr>
            </thead>
            <tbody>
              {submissions.map((s, i) => (
                <tr key={i} style={{ borderBottom: `1px solid ${COLORS.border}22`, transition: "background 0.15s" }}>
                  <td style={{ padding: "11px 12px", color: COLORS.blueAccent, whiteSpace: "nowrap" }}>{s.id}</td>
                  <td style={{ padding: "11px 12px", color: COLORS.white }}>{s.shipment}</td>
                  <td style={{ padding: "11px 12px", color: COLORS.whiteDim, whiteSpace: "nowrap" }}>{s.lane}</td>
                  <td style={{ padding: "11px 12px", color: COLORS.greenGlow, fontWeight: 700 }}>{s.hs}</td>
                  <td style={{ padding: "11px 12px", color: COLORS.whiteDim, whiteSpace: "nowrap" }}>{s.submitted}</td>
                  <td style={{ padding: "11px 12px", color: COLORS.white }}>{s.broker}</td>
                  <td style={{ padding: "11px 12px" }}><Badge color={statusColor[s.status] || "blue"}>{s.status}</Badge></td>
                  <td style={{ padding: "11px 12px", color: COLORS.whiteDim, maxWidth: 200 }}>{s.response}</td>
                  <td style={{ padding: "11px 12px", color: s.time !== "—" ? COLORS.success : COLORS.whiteDim }}>{s.time}</td>
                </tr>
              ))}
            </tbody>
          </table>
        </div>
      </Card>

      <div style={{ display: "grid", gridTemplateColumns: "1fr 1fr", gap: 20, marginTop: 20 }}>
        <Card>
          <div style={{ color: COLORS.whiteDim, fontSize: 11, letterSpacing: "0.1em", marginBottom: 14 }}>AUDIT LOG</div>
          {[
            ["09:22", "SHP-2024-001 submitted to ICEGATE portal", "success"],
            ["11:30", "SHP-2024-003 held — FDA notice triggered", "danger"],
            ["14:05", "SHP-2024-002 classification revised: 8544.42 → 8544.49", "warning"],
            ["16:40", "SHP-2024-005 queued at Dubai Customs", "blue"],
          ].map(([t, msg, color], i) => (
            <div key={i} style={{ display: "flex", gap: 10, marginBottom: 10, paddingLeft: 12, borderLeft: `2px solid ${COLORS[color] || COLORS.blueAccent}` }}>
              <span style={{ color: COLORS.whiteDim, fontSize: 10, whiteSpace: "nowrap", marginTop: 2 }}>{t}</span>
              <span style={{ color: COLORS.white, fontSize: 12 }}>{msg}</span>
            </div>
          ))}
        </Card>
        <Card>
          <div style={{ color: COLORS.whiteDim, fontSize: 11, letterSpacing: "0.1em", marginBottom: 14 }}>PERFORMANCE METRICS</div>
          {[
            ["Average Clearance Time", "4.2 hours", COLORS.greenGlow],
            ["Manual Processing Reduction", "68%", COLORS.success],
            ["Classification Accuracy", "97.4%", COLORS.blueAccent],
            ["Compliance Pass Rate", "91.7%", COLORS.greenGlow],
          ].map(([label, val, color]) => (
            <div key={label} style={{ display: "flex", justifyContent: "space-between", marginBottom: 12 }}>
              <span style={{ color: COLORS.whiteDim, fontSize: 12 }}>{label}</span>
              <span style={{ color, fontSize: 13, fontWeight: 800, fontFamily: "Georgia, serif" }}>{val}</span>
            </div>
          ))}
        </Card>
      </div>
    </div>
  );
}

// ============================================================
// MAIN APP
// ============================================================
export default function App() {
  const [user, setUser] = useState(null);
  const [page, setPage] = useState("dashboard");

  if (!user) return <LoginPage onLogin={setUser} />;

  const pages = {
    dashboard: <DashboardPage />,
    upload: <UploadPage />,
    hscode: <HSCodePage />,
    compliance: <CompliancePage />,
    documents: <DocumentPage />,
    tracking: <TrackingPage />,
  };

  return (
    <div style={{ background: COLORS.navy, minHeight: "100vh", fontFamily: "monospace" }}>
      <style>{`
        @import url('https://fonts.googleapis.com/css2?family=Playfair+Display:wght@700;800&display=swap');
        * { box-sizing: border-box; }
        @keyframes spin { to { transform: rotate(360deg); } }
        ::-webkit-scrollbar { width: 6px; height: 6px; }
        ::-webkit-scrollbar-track { background: ${COLORS.navyMid}; }
        ::-webkit-scrollbar-thumb { background: ${COLORS.border}; border-radius: 3px; }
        button:hover { filter: brightness(1.1); }
      `}</style>

      <Sidebar active={page} onNav={setPage} user={user} />

      <div style={{ marginLeft: 230, padding: "28px 32px", minHeight: "100vh" }}>
        {/* Top Bar */}
        <div style={{ display: "flex", justifyContent: "space-between", alignItems: "center", marginBottom: 28, paddingBottom: 16, borderBottom: `1px solid ${COLORS.border}` }}>
          <div style={{ display: "flex", gap: 8 }}>
            <Badge color="green">🟢 AI ONLINE</Badge>
            <Badge color="blue">WCO 2022</Badge>
            <Badge color="blue">190+ COUNTRIES</Badge>
          </div>
          <div style={{ display: "flex", gap: 12, alignItems: "center" }}>
            <div style={{ color: COLORS.whiteDim, fontSize: 11 }}>🔔 3 alerts</div>
            <button onClick={() => setUser(null)} style={{ background: "transparent", border: `1px solid ${COLORS.border}`, borderRadius: 6, color: COLORS.whiteDim, padding: "6px 12px", cursor: "pointer", fontSize: 11, fontFamily: "monospace" }}>
              Sign Out
            </button>
          </div>
        </div>

        {pages[page] || <DashboardPage />}
      </div>
    </div>
  );
}
