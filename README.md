<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Graham Signal — Intelligent Value Analysis</title>
<link href="https://fonts.googleapis.com/css2?family=Playfair+Display:ital,wght@0,400;0,600;0,700;1,400;1,600&family=DM+Sans:wght@300;400;500;600&family=DM+Mono:wght@400;500&display=swap" rel="stylesheet">
<!-- Firebase SDK -->
<script type="module">
import { initializeApp }                           from 'https://www.gstatic.com/firebasejs/10.12.2/firebase-app.js';
import { getFirestore, doc, getDoc, setDoc,
         deleteDoc, collection, getDocs }          from 'https://www.gstatic.com/firebasejs/10.12.2/firebase-firestore.js';

// ── Firebase config ──
const firebaseConfig = {
  apiKey:            'AIzaSyCcnOKI31vVDMfm62jsPLfD4kMMGOlH43A',
  authDomain:        'investment-ai-509b8.firebaseapp.com',
  projectId:         'investment-ai-509b8',
  storageBucket:     'investment-ai-509b8.firebasestorage.app',
  messagingSenderId: '544838583019',
  appId:             '1:544838583019:web:68c4a28485f2dad411f4ae'
};

const _fbApp = initializeApp(firebaseConfig);
const _db    = getFirestore(_fbApp);

// ── Expose Firebase helpers to main script ──
window._fb = {
  // Save full portfolio record for one email
  async save(email, records) {
    try {
      await setDoc(doc(_db, 'portfolios', email.toLowerCase()), { records, updatedAt: new Date().toISOString() });
      return true;
    } catch(e) { console.warn('Firebase save failed:', e.message); return false; }
  },

  // Load portfolio for one email → array of records
  async load(email) {
    try {
      const snap = await getDoc(doc(_db, 'portfolios', email.toLowerCase()));
      if (!snap.exists()) return [];
      return snap.data().records || [];
    } catch(e) { console.warn('Firebase load failed:', e.message); return null; } // null = offline
  },

  // Delete portfolio for one email
  async remove(email) {
    try {
      await deleteDoc(doc(_db, 'portfolios', email.toLowerCase()));
      return true;
    } catch(e) { console.warn('Firebase delete failed:', e.message); return false; }
  },

  // Load ALL portfolios (admin only)
  async loadAll() {
    try {
      const snap = await getDocs(collection(_db, 'portfolios'));
      return snap.docs.map(d => ({ email: d.id, records: d.data().records || [], updatedAt: d.data().updatedAt }));
    } catch(e) { console.warn('Firebase loadAll failed:', e.message); return null; }
  }
};

// Signal that Firebase is ready
window._fbReady = true;
window.dispatchEvent(new Event('fb-ready'));
</script>
<style>
:root {
  --bg:        #f5f0e8;
  --bg2:       #ede6d6;
  --surface:   #faf7f2;
  --surface2:  #f0ead8;
  --border:    rgba(60,45,20,0.12);
  --border2:   rgba(60,45,20,0.22);
  --ink:       #1a140a;
  --ink2:      #3d2e18;
  --muted:     rgba(26,20,10,0.45);
  --muted2:    rgba(26,20,10,0.25);
  --gold:      #b8860b;
  --gold-l:    #d4a017;
  --gold-ll:   #f0c040;
  --teal:      #2a6b6b;
  --teal-l:    #3d8f8f;
  --crimson:   #8b1a1a;
  --crimson-l: #b52020;
  --slate:     #3a4a5a;
  --slate-l:   #5a7080;
  --shadow:    rgba(60,45,20,0.12);
  --shadow2:   rgba(60,45,20,0.06);
}

* { box-sizing: border-box; margin: 0; padding: 0; }

body {
  background: var(--bg);
  color: var(--ink);
  font-family: 'DM Sans', sans-serif;
  min-height: 100vh;
  -webkit-font-smoothing: antialiased;
}

#page-home { display: block; }
#page-app  { display: none; }

/* ══ NAV ══ */
.home-nav {
  position: fixed; top: 0; left: 0; right: 0; z-index: 200;
  padding: 18px 48px;
  display: flex; align-items: center; justify-content: space-between;
  background: rgba(245,240,232,0.92);
  backdrop-filter: blur(12px);
  border-bottom: 1px solid var(--border);
}
.home-nav-brand { font-family: 'Playfair Display', serif; font-size: 1.3rem; font-weight: 700; color: var(--ink); letter-spacing: 1px; }
.home-nav-brand em { color: var(--gold); font-style: normal; }
.home-nav-links { display: flex; gap: 32px; align-items: center; }
.home-nav-links a { font-size: 0.82rem; font-weight: 500; color: var(--muted); text-decoration: none; letter-spacing: 0.5px; transition: color 0.2s; }
.home-nav-links a:hover { color: var(--ink); }
.btn-nav { background: var(--ink); color: var(--bg); border: none; border-radius: 6px; padding: 9px 22px; font-size: 0.82rem; font-weight: 600; cursor: pointer; letter-spacing: 0.3px; transition: background 0.2s, transform 0.15s; font-family: 'DM Sans', sans-serif; }
.btn-nav:hover { background: var(--gold); transform: translateY(-1px); }

/* ══ HERO ══ */
.home-hero { min-height: 100vh; display: flex; flex-direction: column; justify-content: center; padding: 140px 48px 100px; position: relative; overflow: hidden; }
.hero-bg-pattern { position: absolute; inset: 0; z-index: 0; background-image: repeating-linear-gradient(0deg, transparent, transparent 39px, var(--border) 39px, var(--border) 40px), repeating-linear-gradient(90deg, transparent, transparent 39px, var(--border) 39px, var(--border) 40px); opacity: 0.5; }
.hero-bg-circle { position: absolute; top: -120px; right: -80px; width: 700px; height: 700px; border-radius: 50%; background: radial-gradient(circle, rgba(184,134,11,0.10) 0%, transparent 70%); z-index: 0; }
.hero-content { position: relative; z-index: 1; max-width: 800px; }
.hero-eyebrow { font-family: 'DM Mono', monospace; font-size: 0.72rem; letter-spacing: 3px; text-transform: uppercase; color: var(--gold); margin-bottom: 24px; display: flex; align-items: center; gap: 12px; }
.hero-eyebrow::before { content: ''; display: inline-block; width: 32px; height: 1px; background: var(--gold); }
.hero-title { font-family: 'Playfair Display', serif; font-size: clamp(3.2rem, 7vw, 5.5rem); font-weight: 700; line-height: 1.08; color: var(--ink); margin-bottom: 28px; }
.hero-title em { color: var(--gold); font-style: italic; }
.hero-subtitle { font-size: 1.15rem; font-weight: 300; line-height: 1.7; color: var(--ink2); max-width: 560px; margin-bottom: 44px; }
.hero-ctas { display: flex; gap: 16px; flex-wrap: wrap; align-items: center; }
.btn-primary { background: var(--ink); color: var(--bg); border: none; border-radius: 8px; padding: 16px 36px; font-size: 0.95rem; font-weight: 600; cursor: pointer; letter-spacing: 0.3px; transition: all 0.2s; font-family: 'DM Sans', sans-serif; }
.btn-primary:hover { background: var(--gold); transform: translateY(-2px); box-shadow: 0 8px 24px rgba(184,134,11,0.3); }
.btn-secondary { background: transparent; color: var(--ink); border: 1.5px solid var(--border2); border-radius: 8px; padding: 15px 28px; font-size: 0.92rem; font-weight: 500; cursor: pointer; letter-spacing: 0.3px; transition: all 0.2s; font-family: 'DM Sans', sans-serif; }
.btn-secondary:hover { border-color: var(--gold); color: var(--gold); }
.hero-stats { position: relative; z-index: 1; margin-top: 80px; display: flex; gap: 0; flex-wrap: wrap; border-top: 1px solid var(--border2); border-left: 1px solid var(--border2); max-width: 600px; }
.hero-stat { flex: 1; min-width: 120px; padding: 24px 28px; border-right: 1px solid var(--border2); border-bottom: 1px solid var(--border2); }
.hero-stat-val { font-family: 'Playfair Display', serif; font-size: 2rem; font-weight: 700; color: var(--gold); line-height: 1; }
.hero-stat-lbl { font-size: 0.72rem; font-weight: 500; letter-spacing: 1px; text-transform: uppercase; color: var(--muted); margin-top: 6px; }

/* ══ SECTIONS ══ */
.home-section { padding: 100px 48px; }
.home-section-alt { background: var(--bg2); }
.section-label { font-family: 'DM Mono', monospace; font-size: 0.7rem; letter-spacing: 3px; text-transform: uppercase; color: var(--gold); margin-bottom: 20px; display: flex; align-items: center; gap: 12px; }
.section-label::before { content: ''; display: inline-block; width: 24px; height: 1px; background: var(--gold); }
.section-title { font-family: 'Playfair Display', serif; font-size: clamp(1.8rem, 3.5vw, 2.8rem); font-weight: 700; line-height: 1.2; color: var(--ink); margin-bottom: 20px; }
.section-body { font-size: 1rem; font-weight: 300; line-height: 1.8; color: var(--ink2); max-width: 620px; }

.graham-bio-grid { display: grid; grid-template-columns: 1fr 1fr; gap: 60px; align-items: start; max-width: 1100px; margin: 0 auto; }
.graham-pullquote { background: var(--surface); border-left: 4px solid var(--gold); padding: 32px 36px; border-radius: 0 12px 12px 0; margin-top: 36px; }
.graham-pullquote blockquote { font-family: 'Playfair Display', serif; font-size: 1.25rem; font-style: italic; font-weight: 400; line-height: 1.7; color: var(--ink2); }
.graham-pullquote cite { display: block; margin-top: 16px; font-family: 'DM Mono', monospace; font-size: 0.72rem; letter-spacing: 1.5px; color: var(--gold); font-style: normal; }
.graham-bio-right { padding-top: 12px; }
.graham-bio-right p { margin-bottom: 16px; }

.hiw-grid { display: grid; grid-template-columns: repeat(auto-fit, minmax(220px, 1fr)); gap: 0; max-width: 1100px; margin: 48px auto 0; border: 1px solid var(--border2); border-radius: 12px; overflow: hidden; }
.hiw-card { padding: 36px 32px; border-right: 1px solid var(--border2); position: relative; }
.hiw-card:last-child { border-right: none; }
.hiw-num { font-family: 'Playfair Display', serif; font-size: 3.5rem; font-weight: 700; line-height: 1; color: var(--border2); margin-bottom: 20px; }
.hiw-title { font-family: 'DM Sans', sans-serif; font-size: 0.95rem; font-weight: 600; color: var(--ink); margin-bottom: 10px; }
.hiw-body { font-size: 0.85rem; font-weight: 300; line-height: 1.7; color: var(--muted); }

.criteria-grid { display: grid; grid-template-columns: repeat(auto-fill, minmax(300px, 1fr)); gap: 16px; max-width: 1100px; margin: 48px auto 0; }
.criteria-card { background: var(--surface); border: 1px solid var(--border); border-radius: 10px; padding: 24px 28px; transition: border-color 0.2s, transform 0.2s; }
.criteria-card:hover { border-color: var(--gold); transform: translateY(-2px); }
.criteria-num { font-family: 'DM Mono', monospace; font-size: 0.68rem; letter-spacing: 2px; color: var(--gold); margin-bottom: 8px; }
.criteria-name { font-family: 'Playfair Display', serif; font-size: 1rem; font-weight: 600; color: var(--ink); margin-bottom: 8px; }
.criteria-desc { font-size: 0.82rem; font-weight: 300; line-height: 1.6; color: var(--muted); }

.home-cta-section { padding: 100px 48px; text-align: center; background: var(--ink); position: relative; overflow: hidden; }
.home-cta-section::before { content: ''; position: absolute; top: 50%; left: 50%; transform: translate(-50%,-50%); width: 600px; height: 600px; border-radius: 50%; background: radial-gradient(circle, rgba(184,134,11,0.12) 0%, transparent 70%); }
.home-cta-section .section-label { color: var(--gold-l); justify-content: center; }
.home-cta-section .section-label::before { background: var(--gold-l); }
.home-cta-section .section-title { color: var(--bg); margin: 0 auto 20px; text-align: center; }
.home-cta-section p { color: rgba(245,240,232,0.6); max-width: 480px; margin: 0 auto 40px; font-weight: 300; line-height: 1.7; }
.home-disclaimer { padding: 32px 48px; background: var(--bg2); border-top: 1px solid var(--border); }
.home-disclaimer p { font-size: 0.75rem; color: var(--muted); line-height: 1.7; max-width: 900px; margin: 0 auto; text-align: center; }
.home-footer { padding: 32px 48px; border-top: 1px solid var(--border); display: flex; align-items: center; justify-content: space-between; flex-wrap: wrap; gap: 16px; }
.footer-brand { font-family: 'Playfair Display', serif; font-size: 1.1rem; font-weight: 700; color: var(--ink); }
.footer-brand em { color: var(--gold); font-style: normal; }
.footer-note { font-size: 0.75rem; color: var(--muted); }

/* ══ APP HEADER ══ */
#page-app header { padding: 14px 28px; border-bottom: 1px solid var(--border); display: flex; align-items: center; justify-content: space-between; flex-wrap: wrap; gap: 12px; background: var(--surface); position: sticky; top: 0; z-index: 100; box-shadow: 0 2px 16px var(--shadow2); }
.app-brand { display: flex; align-items: center; gap: 12px; }
.app-logo { font-family: 'Playfair Display', serif; font-size: 1.4rem; font-weight: 700; color: var(--ink); letter-spacing: 1px; }
.app-logo em { color: var(--gold); font-style: normal; }
.app-tag { font-family: 'DM Mono', monospace; font-size: 0.6rem; color: var(--muted); letter-spacing: 1px; border: 1px solid var(--border2); padding: 3px 8px; border-radius: 100px; }
.btn-go-home { background: none; border: 1px solid var(--border2); color: var(--muted); border-radius: 6px; padding: 7px 16px; font-size: 0.8rem; font-weight: 500; cursor: pointer; transition: all 0.2s; font-family: 'DM Sans', sans-serif; }
.btn-go-home:hover { border-color: var(--gold); color: var(--gold); }

/* ══ TABS ══ */
.tabs { display: flex; gap: 0; border-bottom: 1px solid var(--border); background: var(--surface); padding: 0 28px; overflow-x: auto; }
.tab-btn { background: none; border: none; color: var(--muted); padding: 14px 20px; font-size: 0.83rem; font-weight: 500; cursor: pointer; border-bottom: 2px solid transparent; margin-bottom: -1px; white-space: nowrap; transition: all 0.2s; font-family: 'DM Sans', sans-serif; letter-spacing: 0.3px; }
.tab-btn:hover { color: var(--ink); }
.tab-btn.active { color: var(--gold); border-bottom-color: var(--gold); font-weight: 600; }
.tab-content { display: none; }
.tab-content.active { display: block; }

/* ══ CONTAINER & PANEL ══ */
.app-container { max-width: 980px; margin: 0 auto; padding: 36px 20px 80px; }
.panel { background: var(--surface); border: 1px solid var(--border); border-radius: 12px; padding: 28px 32px; margin-bottom: 20px; box-shadow: 0 2px 12px var(--shadow2); }
.panel-title { font-family: 'DM Mono', monospace; font-size: 0.68rem; font-weight: 500; letter-spacing: 2.5px; text-transform: uppercase; color: var(--muted); margin-bottom: 20px; display: flex; align-items: center; gap: 8px; }
.panel-title::before { content: ''; display: inline-block; width: 16px; height: 1px; background: var(--gold); }

/* ══ FORM ══ */
.form-row { display: flex; gap: 14px; flex-wrap: wrap; margin-bottom: 18px; }
.form-group { display: flex; flex-direction: column; gap: 6px; flex: 1; min-width: 160px; }
.form-group label { font-size: 0.75rem; font-weight: 600; letter-spacing: 1px; text-transform: uppercase; color: var(--muted); }
.form-group input { background: var(--bg); color: var(--ink); border: 1px solid var(--border2); border-radius: 8px; padding: 11px 14px; font-size: 0.92rem; font-family: 'DM Sans', sans-serif; transition: border-color 0.2s, box-shadow 0.2s; outline: none; }
.form-group input:focus { border-color: var(--gold); box-shadow: 0 0 0 3px rgba(184,134,11,0.12); }
.form-group input::placeholder { color: var(--muted2); }
.btn-analyse { background: var(--ink); color: var(--bg); border: none; border-radius: 8px; padding: 13px 32px; font-size: 0.92rem; font-weight: 600; cursor: pointer; transition: all 0.2s; font-family: 'DM Sans', sans-serif; display: flex; align-items: center; gap: 8px; }
.btn-analyse:hover { background: var(--gold); transform: translateY(-1px); box-shadow: 0 6px 20px rgba(184,134,11,0.3); }
.btn-analyse:disabled { opacity: 0.5; cursor: not-allowed; transform: none; }

/* ══ LOADING ══ */
.loading-state { display: none; text-align: center; padding: 60px 20px; }
.loading-spinner { width: 40px; height: 40px; border-radius: 50%; border: 2px solid var(--border2); border-top-color: var(--gold); animation: spin 0.9s linear infinite; margin: 0 auto 20px; }
@keyframes spin { to { transform: rotate(360deg); } }
.loading-state p { font-size: 0.85rem; color: var(--muted); font-style: italic; }

/* ══ RESULT ══ */
.result-panel { display: none; }
.result-header-grid { display: grid; grid-template-columns: auto 1fr; gap: 32px; align-items: start; margin-bottom: 32px; padding-bottom: 32px; border-bottom: 1px solid var(--border); }
.score-circle { width: 110px; height: 110px; border-radius: 50%; border: 3px solid var(--gold); display: flex; flex-direction: column; align-items: center; justify-content: center; background: var(--bg); flex-shrink: 0; box-shadow: 0 0 0 6px rgba(184,134,11,0.08); }
.score-val { font-family: 'Playfair Display', serif; font-size: 2.4rem; font-weight: 700; color: var(--ink); line-height: 1; }
.score-lbl { font-size: 0.65rem; letter-spacing: 1.5px; text-transform: uppercase; color: var(--muted); margin-top: 4px; }
.result-meta { flex: 1; }
.result-ticker { font-family: 'DM Mono', monospace; font-size: 0.72rem; letter-spacing: 2px; color: var(--gold); margin-bottom: 6px; }
.result-company { font-family: 'Playfair Display', serif; font-size: 1.6rem; font-weight: 700; color: var(--ink); margin-bottom: 12px; }
.verdict-badge { display: inline-block; padding: 6px 16px; border-radius: 100px; font-family: 'DM Mono', monospace; font-size: 0.7rem; font-weight: 500; letter-spacing: 2px; margin-bottom: 12px; }
.verdict-STRONG-BUY  { background: rgba(42,107,107,0.15); color: var(--teal); border: 1px solid rgba(42,107,107,0.35); }
.verdict-BUY         { background: rgba(184,134,11,0.12); color: var(--gold); border: 1px solid rgba(184,134,11,0.3); }
.verdict-HOLD        { background: rgba(58,74,90,0.12); color: var(--slate-l); border: 1px solid rgba(58,74,90,0.25); }
.verdict-SELL        { background: rgba(139,26,26,0.12); color: var(--crimson-l); border: 1px solid rgba(139,26,26,0.25); }
.verdict-AVOID       { background: rgba(139,26,26,0.2); color: var(--crimson); border: 1px solid rgba(139,26,26,0.4); }
.result-date { font-size: 0.75rem; color: var(--muted); }

/* ══ CRITERIA SCORES ══ */
.criteria-scores-grid { display: grid; grid-template-columns: repeat(auto-fill, minmax(280px, 1fr)); gap: 14px; margin-bottom: 20px; }
.criterion-card { background: var(--bg); border: 1px solid var(--border); border-radius: 10px; padding: 18px 20px; transition: border-color 0.2s; }
.criterion-card:hover { border-color: var(--gold); }
.criterion-header { display: flex; justify-content: space-between; align-items: flex-start; margin-bottom: 10px; }
.criterion-name { font-size: 0.82rem; font-weight: 600; color: var(--ink); line-height: 1.3; }
.criterion-score { font-family: 'DM Mono', monospace; font-size: 1.1rem; font-weight: 500; color: var(--gold); flex-shrink: 0; margin-left: 8px; }
.criterion-bar { height: 3px; background: var(--border2); border-radius: 2px; margin-bottom: 8px; }
.criterion-fill { height: 100%; border-radius: 2px; transition: width 0.6s ease; }
.criterion-comment { font-size: 0.78rem; color: var(--muted); line-height: 1.5; }

/* ══ HORIZONS ══ */
.horizons-row { display: grid; grid-template-columns: repeat(3, 1fr); gap: 14px; margin-bottom: 20px; }
.horizon-card { background: var(--bg); border: 1px solid var(--border); border-radius: 10px; padding: 18px 20px; text-align: center; }
.horizon-label { font-family: 'DM Mono', monospace; font-size: 0.68rem; letter-spacing: 2px; text-transform: uppercase; color: var(--muted); margin-bottom: 8px; }
.horizon-rating { font-family: 'Playfair Display', serif; font-size: 1.1rem; font-weight: 700; color: var(--ink); margin-bottom: 6px; }
.horizon-stars { color: var(--gold); font-size: 0.85rem; letter-spacing: 2px; margin-bottom: 8px; }
.horizon-note { font-size: 0.75rem; color: var(--muted); line-height: 1.5; }

/* ══ BUFFETT COMPARISON ══ */
.buffett-comparison { background: var(--bg2); border: 1px solid var(--border2); border-radius: 10px; padding: 24px 28px; }
.buffett-comparison-title { font-family: 'DM Mono', monospace; font-size: 0.68rem; letter-spacing: 2px; text-transform: uppercase; color: var(--muted); margin-bottom: 16px; }
.comparison-scores { display: flex; gap: 12px; align-items: center; justify-content: space-between; margin-top: 16px; padding-top: 16px; border-top: 1px solid var(--border); }
.comp-score-block { text-align: center; flex: 1; }
.comp-score-val { font-family: 'Playfair Display', serif; font-size: 2rem; font-weight: 700; }
.comp-score-val.gs  { color: var(--gold); }
.comp-score-val.buf { color: var(--teal); }
.comp-score-name { font-size: 0.72rem; color: var(--muted); margin-top: 4px; }
.comp-divider { width: 1px; height: 50px; background: var(--border2); }
.comparison-summary { font-size: 0.83rem; color: var(--muted); line-height: 1.6; margin-top: 12px; }

/* ══ NARRATIVE ══ */
.narrative-text { font-family: 'Playfair Display', serif; font-size: 1.05rem; font-weight: 400; font-style: italic; line-height: 1.9; color: var(--ink2); border-left: 3px solid var(--gold); padding-left: 24px; }
.graham-quote-box { background: var(--bg2); border-radius: 10px; padding: 24px 28px; margin-top: 20px; }
.graham-quote-text { font-family: 'Playfair Display', serif; font-size: 1rem; font-style: italic; line-height: 1.7; color: var(--ink2); }
.graham-quote-source { font-family: 'DM Mono', monospace; font-size: 0.68rem; letter-spacing: 1.5px; color: var(--gold); margin-top: 12px; }

/* ══ TAGS & NOTES ══ */
.tag-row { display: flex; gap: 8px; flex-wrap: wrap; margin-bottom: 12px; }
.stock-tag { padding: 5px 14px; border-radius: 100px; font-size: 0.72rem; font-weight: 600; cursor: pointer; border: 1.5px solid; transition: all 0.2s; font-family: 'DM Sans', sans-serif; letter-spacing: 0.5px; background: transparent; }
.stock-tag.watch  { color: var(--slate-l); border-color: var(--slate-l); }
.stock-tag.watch.active  { background: var(--slate-l); color: white; }
.stock-tag.bought { color: var(--teal); border-color: var(--teal); }
.stock-tag.bought.active { background: var(--teal); color: white; }
.stock-tag.sold   { color: var(--crimson-l); border-color: var(--crimson-l); }
.stock-tag.sold.active   { background: var(--crimson-l); color: white; }
.notes-area { width: 100%; background: var(--bg); color: var(--ink); border: 1px solid var(--border2); border-radius: 8px; padding: 12px 14px; font-size: 0.88rem; font-family: 'DM Sans', sans-serif; resize: vertical; min-height: 80px; line-height: 1.6; transition: border-color 0.2s; outline: none; }
.notes-area:focus { border-color: var(--gold); }
.btn-save-notes { background: none; border: 1px solid var(--border2); color: var(--muted); border-radius: 6px; padding: 7px 18px; font-size: 0.8rem; font-weight: 500; cursor: pointer; margin-top: 8px; transition: all 0.2s; font-family: 'DM Sans', sans-serif; }
.btn-save-notes:hover { border-color: var(--gold); color: var(--gold); }
.success-msg { background: rgba(42,107,107,0.08); border: 1px solid rgba(42,107,107,0.2); color: var(--teal); padding: 10px 16px; border-radius: 8px; font-size: 0.82rem; margin-top: 8px; display: none; }
.section-divider { height: 1px; background: var(--border); margin: 24px 0; }

/* ══ PORTFOLIO ══ */
.portfolio-login-wrap { text-align: center; padding: 60px 20px; }
.portfolio-login-wrap h2 { font-family: 'Playfair Display', serif; font-size: 1.6rem; font-weight: 700; color: var(--ink); margin-bottom: 12px; }
.portfolio-login-wrap p { font-size: 0.9rem; color: var(--muted); margin-bottom: 28px; line-height: 1.7; max-width: 440px; margin-left: auto; margin-right: auto; }
.login-row { display: flex; gap: 10px; justify-content: center; max-width: 420px; margin: 0 auto; }
.login-row input { flex: 1; background: var(--bg); color: var(--ink); border: 1px solid var(--border2); border-radius: 8px; padding: 12px 16px; font-size: 0.92rem; font-family: 'DM Sans', sans-serif; outline: none; transition: border-color 0.2s; }
.login-row input:focus { border-color: var(--gold); }
.btn-login { background: var(--ink); color: var(--bg); border: none; border-radius: 8px; padding: 12px 24px; font-size: 0.88rem; font-weight: 600; cursor: pointer; transition: all 0.2s; font-family: 'DM Sans', sans-serif; }
.btn-login:hover { background: var(--gold); }

#portfolio-view { display: none; }
.portfolio-header { display: flex; align-items: center; justify-content: space-between; flex-wrap: wrap; gap: 16px; margin-bottom: 24px; }
.portfolio-identity h3 { font-family: 'Playfair Display', serif; font-size: 1.3rem; font-weight: 700; color: var(--ink); }
.portfolio-identity p { font-size: 0.8rem; color: var(--muted); margin-top: 4px; }
.btn-logout { background: none; border: 1px solid var(--border2); color: var(--muted); border-radius: 6px; padding: 7px 16px; font-size: 0.78rem; font-weight: 500; cursor: pointer; transition: all 0.2s; font-family: 'DM Sans', sans-serif; }
.btn-logout:hover { border-color: var(--crimson-l); color: var(--crimson-l); }

.portfolio-stats { display: grid; grid-template-columns: repeat(auto-fill, minmax(150px, 1fr)); gap: 12px; margin-bottom: 24px; }
.stat-card { background: var(--surface); border: 1px solid var(--border); border-radius: 10px; padding: 18px 20px; }
.stat-val { font-family: 'Playfair Display', serif; font-size: 1.8rem; font-weight: 700; color: var(--gold); }
.stat-lbl { font-size: 0.72rem; color: var(--muted); margin-top: 4px; text-transform: uppercase; letter-spacing: 1px; }

.portfolio-filters { display: flex; gap: 8px; flex-wrap: wrap; margin-bottom: 16px; }
.filter-btn { background: none; border: 1px solid var(--border2); color: var(--muted); border-radius: 100px; padding: 6px 16px; font-size: 0.78rem; font-weight: 500; cursor: pointer; transition: all 0.2s; font-family: 'DM Sans', sans-serif; }
.filter-btn:hover, .filter-btn.active { border-color: var(--gold); color: var(--gold); background: rgba(184,134,11,0.08); }

.portfolio-list { display: flex; flex-direction: column; gap: 12px; }
.portfolio-item { background: var(--surface); border: 1px solid var(--border); border-radius: 10px; padding: 18px 22px; cursor: pointer; transition: all 0.2s; display: grid; grid-template-columns: 1fr auto; gap: 12px; align-items: center; }
.portfolio-item:hover { border-color: var(--gold); transform: translateY(-1px); box-shadow: 0 4px 16px var(--shadow); }
.pi-ticker { font-family: 'DM Mono', monospace; font-size: 0.72rem; letter-spacing: 2px; color: var(--gold); }
.pi-company { font-weight: 600; color: var(--ink); margin-top: 2px; }
.pi-meta { font-size: 0.75rem; color: var(--muted); margin-top: 4px; display: flex; gap: 12px; flex-wrap: wrap; align-items: center; }
.pi-tag-badge { padding: 2px 10px; border-radius: 100px; font-size: 0.65rem; font-weight: 600; letter-spacing: 0.5px; }
.pi-tag-watch  { background: rgba(58,74,90,0.12); color: var(--slate-l); }
.pi-tag-bought { background: rgba(42,107,107,0.12); color: var(--teal); }
.pi-tag-sold   { background: rgba(139,26,26,0.12); color: var(--crimson-l); }
.pi-score { font-family: 'Playfair Display', serif; font-size: 1.6rem; font-weight: 700; color: var(--ink); text-align: right; }
.pi-verdict { font-size: 0.68rem; letter-spacing: 1px; color: var(--muted); margin-top: 2px; text-align: right; }
.empty-portfolio { text-align: center; padding: 60px 20px; color: var(--muted); }
.empty-portfolio p { font-style: italic; }

/* ══ PRINCIPLES ══ */
.principles-intro { background: var(--bg2); border: 1px solid var(--border); border-radius: 10px; padding: 24px 28px; margin-bottom: 24px; }
.principles-intro p { font-size: 0.9rem; line-height: 1.8; color: var(--ink2); }
.principles-list { display: flex; flex-direction: column; gap: 16px; }

.guide-principle { border-radius: 12px; padding: 28px 32px; border: 1px solid; position: relative; overflow: hidden; }
.guide-principle:nth-child(3n+1) { background: linear-gradient(135deg, #faf6ec 0%, #f5edd8 100%); border-color: rgba(184,134,11,0.35); }
.guide-principle:nth-child(3n+1)::before { content: ''; position: absolute; top: 0; left: 0; right: 0; height: 3px; background: var(--gold); }
.guide-principle:nth-child(3n+1) .guide-num { color: var(--gold); }
.guide-principle:nth-child(3n+2) { background: linear-gradient(135deg, #edf6f6 0%, #daeee8 100%); border-color: rgba(42,107,107,0.3); }
.guide-principle:nth-child(3n+2)::before { content: ''; position: absolute; top: 0; left: 0; right: 0; height: 3px; background: var(--teal); }
.guide-principle:nth-child(3n+2) .guide-num { color: var(--teal); }
.guide-principle:nth-child(3n+3) { background: linear-gradient(135deg, #eef0f4 0%, #e2e6ee 100%); border-color: rgba(58,74,90,0.25); }
.guide-principle:nth-child(3n+3)::before { content: ''; position: absolute; top: 0; left: 0; right: 0; height: 3px; background: var(--slate); }
.guide-principle:nth-child(3n+3) .guide-num { color: var(--slate); }

.guide-num { font-family: 'DM Mono', monospace; font-size: 0.68rem; letter-spacing: 2.5px; text-transform: uppercase; margin-bottom: 6px; }
.guide-title { font-family: 'Playfair Display', serif; font-size: 1.15rem; font-weight: 700; color: var(--ink); margin-bottom: 10px; }
.guide-body { font-size: 0.88rem; line-height: 1.8; color: var(--ink2); margin-bottom: 16px; }
.guide-bullets { list-style: none; padding: 0; display: flex; flex-direction: column; gap: 6px; }
.guide-bullets li { font-size: 0.82rem; color: var(--ink2); padding-left: 20px; position: relative; line-height: 1.6; }
.guide-bullets li::before { content: '→'; position: absolute; left: 0; color: inherit; opacity: 0.6; }
.guide-modern-note { margin-top: 14px; padding: 12px 16px; background: rgba(255,255,255,0.5); border-radius: 8px; font-size: 0.8rem; color: var(--muted); font-style: italic; line-height: 1.6; }
.guide-modern-note strong { font-style: normal; color: var(--ink2); }

/* ══ ERRORS ══ */
.error-msg { background: rgba(139,26,26,0.08); border: 1px solid rgba(139,26,26,0.2); color: var(--crimson-l); padding: 14px 18px; border-radius: 8px; font-size: 0.85rem; margin-top: 16px; display: none; line-height: 1.6; }

/* ══ HOLDINGS TABLE ══ */
.holdings-section { margin-top: 0; border-top: 1px solid var(--border); }
.holdings-toggle {
  width: 100%; background: none; border: none; padding: 12px 22px;
  display: flex; align-items: center; justify-content: space-between;
  cursor: pointer; font-family: 'DM Sans', sans-serif;
  font-size: 0.78rem; font-weight: 600; color: var(--muted);
  letter-spacing: 0.5px; text-transform: uppercase; transition: color 0.2s;
}
.holdings-toggle:hover { color: var(--gold); }
.holdings-toggle .ht-arrow { font-size: 0.65rem; transition: transform 0.2s; }
.holdings-toggle.open .ht-arrow { transform: rotate(180deg); }
.holdings-toggle .ht-summary { font-size: 0.72rem; color: var(--muted2); font-weight: 400; text-transform: none; letter-spacing: 0; }

.holdings-body { display: none; padding: 0 22px 18px; }
.holdings-body.open { display: block; }

/* Add lot form */
.add-lot-form {
  background: var(--bg2); border: 1px solid var(--border);
  border-radius: 8px; padding: 16px 18px; margin-bottom: 14px;
  display: none;
}
.add-lot-form.open { display: block; }
.lot-form-row { display: flex; gap: 10px; flex-wrap: wrap; margin-bottom: 10px; }
.lot-form-group { display: flex; flex-direction: column; gap: 4px; flex: 1; min-width: 100px; }
.lot-form-group label { font-size: 0.68rem; font-weight: 600; letter-spacing: 1px; text-transform: uppercase; color: var(--muted); }
.lot-form-group input, .lot-form-group select {
  background: var(--surface); color: var(--ink);
  border: 1px solid var(--border2); border-radius: 6px;
  padding: 8px 10px; font-size: 0.85rem; font-family: 'DM Sans', sans-serif;
  outline: none; transition: border-color 0.2s;
}
.lot-form-group input:focus, .lot-form-group select:focus { border-color: var(--gold); }
.lot-form-actions { display: flex; gap: 8px; }
.btn-lot-save {
  background: var(--ink); color: var(--bg); border: none; border-radius: 6px;
  padding: 8px 20px; font-size: 0.82rem; font-weight: 600; cursor: pointer;
  font-family: 'DM Sans', sans-serif; transition: background 0.2s;
}
.btn-lot-save:hover { background: var(--gold); }
.btn-lot-cancel {
  background: none; border: 1px solid var(--border2); color: var(--muted);
  border-radius: 6px; padding: 8px 16px; font-size: 0.82rem; font-weight: 500;
  cursor: pointer; font-family: 'DM Sans', sans-serif; transition: all 0.2s;
}
.btn-lot-cancel:hover { border-color: var(--crimson-l); color: var(--crimson-l); }

/* Holdings table */
.holdings-table-wrap { overflow-x: auto; }
.holdings-table {
  width: 100%; border-collapse: collapse; font-size: 0.8rem;
}
.holdings-table th {
  font-family: 'DM Mono', monospace; font-size: 0.62rem; letter-spacing: 1.5px;
  text-transform: uppercase; color: var(--muted); font-weight: 500;
  padding: 8px 10px; text-align: right; border-bottom: 1px solid var(--border2);
  white-space: nowrap;
}
.holdings-table th:first-child { text-align: left; }
.holdings-table td {
  padding: 9px 10px; text-align: right; border-bottom: 1px solid var(--border);
  color: var(--ink2); font-size: 0.82rem; vertical-align: middle;
}
.holdings-table td:first-child { text-align: left; font-family: 'DM Mono', monospace; font-size: 0.75rem; color: var(--muted); }
.holdings-table tbody tr:last-child td { border-bottom: none; }
.holdings-table tbody tr:hover td { background: var(--bg2); }

.pnl-pos { color: var(--teal); font-weight: 600; }
.pnl-neg { color: var(--crimson-l); font-weight: 600; }
.pnl-neu { color: var(--muted); }

/* Holdings total row */
.holdings-total td {
  font-weight: 700; border-top: 2px solid var(--border2) !important;
  border-bottom: none !important; background: var(--bg2) !important;
  font-size: 0.82rem;
}

/* Refresh & actions bar */
.holdings-actions {
  display: flex; align-items: center; justify-content: space-between;
  flex-wrap: wrap; gap: 8px; margin-bottom: 10px;
}
.btn-add-lot {
  background: none; border: 1px dashed var(--border2); color: var(--muted);
  border-radius: 6px; padding: 6px 14px; font-size: 0.78rem; font-weight: 500;
  cursor: pointer; font-family: 'DM Sans', sans-serif; transition: all 0.2s;
}
.btn-add-lot:hover { border-color: var(--gold); color: var(--gold); }
.btn-refresh-price {
  background: none; border: 1px solid var(--border2); color: var(--muted);
  border-radius: 6px; padding: 6px 14px; font-size: 0.78rem; font-weight: 500;
  cursor: pointer; font-family: 'DM Sans', sans-serif; transition: all 0.2s;
  display: flex; align-items: center; gap: 6px;
}
.btn-refresh-price:hover { border-color: var(--teal); color: var(--teal); }
.btn-refresh-price.loading { opacity: 0.5; pointer-events: none; }
.price-as-of { font-size: 0.68rem; color: var(--muted2); font-style: italic; }

/* delete lot btn */
.btn-del-lot {
  background: none; border: none; color: var(--muted2); cursor: pointer;
  font-size: 0.75rem; padding: 2px 6px; border-radius: 4px;
  transition: color 0.2s; font-family: 'DM Sans', sans-serif;
}
.btn-del-lot:hover { color: var(--crimson-l); }

/* Portfolio refresh all */
.btn-refresh-all {
  background: var(--surface); border: 1px solid var(--border2); color: var(--ink2);
  border-radius: 8px; padding: 9px 20px; font-size: 0.82rem; font-weight: 600;
  cursor: pointer; font-family: 'DM Sans', sans-serif; transition: all 0.2s;
  display: flex; align-items: center; gap: 8px;
}
.btn-refresh-all:hover { border-color: var(--gold); color: var(--gold); }
.refresh-all-status { font-size: 0.75rem; color: var(--muted); font-style: italic; }

/* ══ ADMIN PAGE ══ */
#page-admin { display: none; min-height: 100vh; background: #0d0d0f; color: #e8e4dc; font-family: 'DM Sans', sans-serif; }

/* Admin nav */
.admin-nav {
  padding: 14px 32px; background: #141418; border-bottom: 1px solid rgba(184,134,11,0.2);
  display: flex; align-items: center; justify-content: space-between; flex-wrap: wrap; gap: 12px;
  position: sticky; top: 0; z-index: 100;
}
.admin-nav-brand { display: flex; align-items: center; gap: 14px; }
.admin-nav-logo { font-family: 'Playfair Display', serif; font-size: 1.1rem; font-weight: 700; color: #e8e4dc; }
.admin-nav-logo em { color: var(--gold); font-style: normal; }
.admin-badge {
  font-family: 'DM Mono', monospace; font-size: 0.6rem; letter-spacing: 2px;
  background: rgba(184,134,11,0.15); color: var(--gold); border: 1px solid rgba(184,134,11,0.3);
  padding: 3px 10px; border-radius: 100px;
}
.admin-nav-right { display: flex; align-items: center; gap: 12px; }
.admin-nav-user { font-size: 0.78rem; color: rgba(232,228,220,0.45); }
.btn-admin-logout {
  background: none; border: 1px solid rgba(232,228,220,0.15); color: rgba(232,228,220,0.5);
  border-radius: 6px; padding: 6px 14px; font-size: 0.78rem; cursor: pointer;
  font-family: 'DM Sans', sans-serif; transition: all 0.2s;
}
.btn-admin-logout:hover { border-color: var(--crimson-l); color: var(--crimson-l); }
.btn-back-site {
  background: none; border: 1px solid rgba(184,134,11,0.3); color: var(--gold-l);
  border-radius: 6px; padding: 6px 14px; font-size: 0.78rem; cursor: pointer;
  font-family: 'DM Sans', sans-serif; transition: all 0.2s;
}
.btn-back-site:hover { background: rgba(184,134,11,0.1); }

/* Admin login */
#admin-login {
  min-height: calc(100vh - 57px); display: flex; align-items: center; justify-content: center;
  padding: 40px 20px;
}
.admin-login-card {
  background: #141418; border: 1px solid rgba(184,134,11,0.2); border-radius: 14px;
  padding: 48px 44px; width: 100%; max-width: 420px;
  box-shadow: 0 20px 60px rgba(0,0,0,0.5);
}
.admin-login-icon { font-size: 2rem; margin-bottom: 20px; }
.admin-login-card h2 {
  font-family: 'Playfair Display', serif; font-size: 1.5rem; font-weight: 700;
  color: #e8e4dc; margin-bottom: 6px;
}
.admin-login-card p { font-size: 0.85rem; color: rgba(232,228,220,0.45); margin-bottom: 32px; line-height: 1.6; }
.admin-field { display: flex; flex-direction: column; gap: 6px; margin-bottom: 16px; }
.admin-field label { font-size: 0.72rem; font-weight: 600; letter-spacing: 1.5px; text-transform: uppercase; color: rgba(232,228,220,0.45); }
.admin-field input {
  background: #0d0d0f; color: #e8e4dc; border: 1px solid rgba(232,228,220,0.12);
  border-radius: 8px; padding: 12px 14px; font-size: 0.92rem; font-family: 'DM Sans', sans-serif;
  outline: none; transition: border-color 0.2s;
}
.admin-field input:focus { border-color: var(--gold); }
.admin-field input::placeholder { color: rgba(232,228,220,0.2); }
.btn-admin-login {
  width: 100%; background: var(--gold); color: #0d0d0f; border: none; border-radius: 8px;
  padding: 13px; font-size: 0.92rem; font-weight: 700; cursor: pointer; margin-top: 8px;
  font-family: 'DM Sans', sans-serif; letter-spacing: 0.5px; transition: all 0.2s;
}
.btn-admin-login:hover { background: var(--gold-l); transform: translateY(-1px); }
.admin-login-err {
  background: rgba(139,26,26,0.15); border: 1px solid rgba(139,26,26,0.3); color: #e07070;
  padding: 10px 14px; border-radius: 8px; font-size: 0.82rem; margin-top: 12px; display: none;
}
.admin-forgot {
  text-align: center; margin-top: 20px; font-size: 0.8rem; color: rgba(232,228,220,0.35);
}
.admin-forgot a { color: var(--gold-l); cursor: pointer; text-decoration: none; }
.admin-forgot a:hover { text-decoration: underline; }

/* Reset code panel */
#admin-reset-panel { display: none; margin-top: 20px; }
.admin-reset-info {
  background: rgba(42,107,107,0.12); border: 1px solid rgba(42,107,107,0.25);
  border-radius: 8px; padding: 14px 16px; font-size: 0.82rem; color: #7ec8c8;
  margin-bottom: 14px; line-height: 1.6;
}
.admin-field-row { display: flex; gap: 8px; }
.admin-field-row input { flex: 1; }
.btn-verify-code {
  background: var(--teal); color: white; border: none; border-radius: 8px;
  padding: 12px 18px; font-size: 0.85rem; font-weight: 600; cursor: pointer;
  font-family: 'DM Sans', sans-serif; transition: background 0.2s; white-space: nowrap;
}
.btn-verify-code:hover { background: var(--teal-l); }

/* Admin console */
#admin-console { display: none; }
.admin-container { max-width: 1200px; margin: 0 auto; padding: 32px 24px 80px; }

/* Admin stats bar */
.admin-stats-bar {
  display: grid; grid-template-columns: repeat(auto-fill, minmax(160px, 1fr));
  gap: 12px; margin-bottom: 28px;
}
.admin-stat {
  background: #141418; border: 1px solid rgba(184,134,11,0.15); border-radius: 10px;
  padding: 18px 20px;
}
.admin-stat-val { font-family: 'Playfair Display', serif; font-size: 2rem; font-weight: 700; color: var(--gold); line-height: 1; }
.admin-stat-lbl { font-size: 0.7rem; color: rgba(232,228,220,0.4); margin-top: 5px; text-transform: uppercase; letter-spacing: 1px; }

/* Account list */
.admin-section-title {
  font-family: 'DM Mono', monospace; font-size: 0.68rem; letter-spacing: 3px;
  text-transform: uppercase; color: rgba(184,134,11,0.7); margin-bottom: 16px;
  display: flex; align-items: center; gap: 10px;
}
.admin-section-title::before { content: ''; display: inline-block; width: 20px; height: 1px; background: var(--gold); }

.admin-search-row { display: flex; gap: 10px; margin-bottom: 16px; flex-wrap: wrap; }
.admin-search {
  flex: 1; min-width: 200px; background: #141418; color: #e8e4dc;
  border: 1px solid rgba(232,228,220,0.12); border-radius: 8px;
  padding: 10px 14px; font-size: 0.88rem; font-family: 'DM Sans', sans-serif; outline: none;
  transition: border-color 0.2s;
}
.admin-search:focus { border-color: var(--gold); }
.admin-search::placeholder { color: rgba(232,228,220,0.2); }

/* Account cards */
.admin-accounts-list { display: flex; flex-direction: column; gap: 10px; }
.admin-account-card {
  background: #141418; border: 1px solid rgba(232,228,220,0.08);
  border-radius: 10px; overflow: hidden; transition: border-color 0.2s;
}
.admin-account-card:hover { border-color: rgba(184,134,11,0.3); }
.admin-account-header {
  display: grid; grid-template-columns: 1fr auto; gap: 12px; align-items: center;
  padding: 16px 20px; cursor: pointer;
}
.admin-account-email { font-size: 0.9rem; font-weight: 600; color: #e8e4dc; }
.admin-account-meta { font-size: 0.75rem; color: rgba(232,228,220,0.35); margin-top: 3px; }
.admin-account-actions { display: flex; align-items: center; gap: 8px; }
.admin-expand-btn {
  background: none; border: 1px solid rgba(232,228,220,0.12); color: rgba(232,228,220,0.4);
  border-radius: 6px; padding: 5px 12px; font-size: 0.75rem; cursor: pointer;
  font-family: 'DM Sans', sans-serif; transition: all 0.2s;
}
.admin-expand-btn:hover { border-color: var(--gold); color: var(--gold); }
.btn-admin-export {
  background: none; border: 1px solid rgba(42,107,107,0.35); color: #7ec8c8;
  border-radius: 6px; padding: 5px 12px; font-size: 0.75rem; cursor: pointer;
  font-family: 'DM Sans', sans-serif; transition: all 0.2s;
}
.btn-admin-export:hover { background: rgba(42,107,107,0.12); }
.btn-admin-delete {
  background: none; border: 1px solid rgba(139,26,26,0.3); color: #e07070;
  border-radius: 6px; padding: 5px 12px; font-size: 0.75rem; cursor: pointer;
  font-family: 'DM Sans', sans-serif; transition: all 0.2s;
}
.btn-admin-delete:hover { background: rgba(139,26,26,0.15); }

/* Account portfolio detail */
.admin-account-detail { display: none; border-top: 1px solid rgba(232,228,220,0.06); }
.admin-account-detail.open { display: block; }
.admin-portfolio-inner { padding: 20px; }

/* Admin stock rows */
.admin-stock-list { display: flex; flex-direction: column; gap: 8px; }
.admin-stock-row {
  background: #0d0d0f; border: 1px solid rgba(232,228,220,0.07);
  border-radius: 8px; padding: 14px 18px;
  display: grid; grid-template-columns: 80px 1fr auto auto auto; gap: 12px; align-items: center;
}
.admin-stock-ticker { font-family: 'DM Mono', monospace; font-size: 0.72rem; letter-spacing: 2px; color: var(--gold); }
.admin-stock-company { font-size: 0.85rem; color: #e8e4dc; font-weight: 500; }
.admin-stock-date { font-size: 0.72rem; color: rgba(232,228,220,0.35); }
.admin-score-pill {
  font-family: 'DM Mono', monospace; font-size: 0.75rem; font-weight: 600;
  padding: 3px 10px; border-radius: 100px; border: 1px solid;
}
.admin-score-high { color: #7ec8c8; border-color: rgba(42,107,107,0.4); background: rgba(42,107,107,0.1); }
.admin-score-mid  { color: var(--gold-l); border-color: rgba(184,134,11,0.35); background: rgba(184,134,11,0.08); }
.admin-score-low  { color: #e07070; border-color: rgba(139,26,26,0.35); background: rgba(139,26,26,0.1); }
.admin-tag-pill {
  font-size: 0.68rem; padding: 2px 10px; border-radius: 100px; font-weight: 600;
}
.admin-tag-watch  { background: rgba(58,74,90,0.2);  color: #8aa0b0; }
.admin-tag-bought { background: rgba(42,107,107,0.2); color: #7ec8c8; }
.admin-tag-sold   { background: rgba(139,26,26,0.2);  color: #e07070; }

/* Admin holdings mini-table */
.admin-holdings-mini { margin-top: 8px; overflow-x: auto; }
.admin-holdings-mini table { width: 100%; border-collapse: collapse; font-size: 0.75rem; }
.admin-holdings-mini th {
  font-family: 'DM Mono', monospace; font-size: 0.6rem; letter-spacing: 1.5px; text-transform: uppercase;
  color: rgba(232,228,220,0.3); padding: 6px 8px; text-align: right; border-bottom: 1px solid rgba(232,228,220,0.07);
}
.admin-holdings-mini th:first-child { text-align: left; }
.admin-holdings-mini td { padding: 6px 8px; text-align: right; color: rgba(232,228,220,0.6); border-bottom: 1px solid rgba(232,228,220,0.04); }
.admin-holdings-mini td:first-child { text-align: left; font-family: 'DM Mono', monospace; font-size: 0.68rem; }
.admin-holdings-mini tbody tr:last-child td { border-bottom: none; }

.admin-empty { text-align: center; padding: 40px; color: rgba(232,228,220,0.25); font-style: italic; font-size: 0.85rem; }
.admin-no-holdings { font-size: 0.75rem; color: rgba(232,228,220,0.25); font-style: italic; margin-top: 6px; }

/* EmailJS config panel inside admin */
.admin-ejs-panel {
  background: #0d0d0f; border: 1px solid rgba(184,134,11,0.15); border-radius: 10px;
  padding: 20px 24px; margin-bottom: 24px;
}
.admin-ejs-title { font-family: 'DM Mono', monospace; font-size: 0.65rem; letter-spacing: 2px; text-transform: uppercase; color: rgba(184,134,11,0.6); margin-bottom: 14px; }
.admin-ejs-row { display: flex; gap: 10px; flex-wrap: wrap; margin-bottom: 10px; }
.admin-ejs-group { display: flex; flex-direction: column; gap: 5px; flex: 1; min-width: 160px; }
.admin-ejs-group label { font-size: 0.68rem; letter-spacing: 1px; text-transform: uppercase; color: rgba(232,228,220,0.35); font-weight: 600; }
.admin-ejs-group input {
  background: #141418; color: #e8e4dc; border: 1px solid rgba(232,228,220,0.12);
  border-radius: 6px; padding: 8px 12px; font-size: 0.82rem; font-family: 'DM Mono', monospace; outline: none;
  transition: border-color 0.2s;
}
.admin-ejs-group input:focus { border-color: var(--gold); }
.btn-save-ejs {
  background: rgba(184,134,11,0.15); color: var(--gold-l); border: 1px solid rgba(184,134,11,0.3);
  border-radius: 6px; padding: 8px 20px; font-size: 0.82rem; font-weight: 600; cursor: pointer;
  font-family: 'DM Sans', sans-serif; transition: all 0.2s;
}
.btn-save-ejs:hover { background: rgba(184,134,11,0.25); }
.ejs-status { font-size: 0.75rem; color: #7ec8c8; margin-top: 8px; display: none; }

/* ══ RESPONSIVE ══ */
@media (max-width: 700px) {
  .home-nav { padding: 14px 20px; }
  .home-nav-links a { display: none; }
  .home-hero { padding: 120px 20px 80px; }
  .home-section { padding: 60px 20px; }
  .graham-bio-grid { grid-template-columns: 1fr; gap: 32px; }
  .hiw-card { border-right: none; border-bottom: 1px solid var(--border2); }
  .result-header-grid { grid-template-columns: 1fr; }
  .horizons-row { grid-template-columns: 1fr; }
  .home-footer { flex-direction: column; text-align: center; }
  .home-cta-section { padding: 60px 20px; }
  .panel { padding: 20px 18px; }
}
</style>
</head>
<body>

<!-- ══════════════ HOME PAGE ══════════════ -->
<div id="page-home">

  <nav class="home-nav">
    <div class="home-nav-brand">Graham <em>Signal</em></div>
    <div class="home-nav-links">
      <a href="#how-it-works" onclick="smoothScroll('how-it-works')">Method</a>
      <a href="#criteria" onclick="smoothScroll('criteria')">Criteria</a>
      <a href="#graham" onclick="smoothScroll('graham')">Graham</a>
      <button class="btn-nav" onclick="enterApp()">Open Tool →</button>
    </div>
  </nav>

  <section class="home-hero">
    <div class="hero-bg-pattern"></div>
    <div class="hero-bg-circle"></div>
    <div class="hero-content">
      <div class="hero-eyebrow">Benjamin Graham · Value Investing · Est. 1949</div>
      <h1 class="hero-title">Analyse stocks the<br><em>father of value</em><br>investing way.</h1>
      <p class="hero-subtitle">Graham Signal applies Benjamin Graham's ten fundamental criteria — rigorously modernised — to provide an evidence-based, academically grounded assessment of any stock's intrinsic worth.</p>
      <div class="hero-ctas">
        <button class="btn-primary" onclick="enterApp()">Begin Analysis →</button>
        <button class="btn-secondary" onclick="document.getElementById('graham').scrollIntoView({behavior:'smooth'})">About Graham</button>
      </div>
    </div>
    <div class="hero-stats">
      <div class="hero-stat"><div class="hero-stat-val">10</div><div class="hero-stat-lbl">Graham Criteria</div></div>
      <div class="hero-stat"><div class="hero-stat-val">3</div><div class="hero-stat-lbl">Time Horizons</div></div>
      <div class="hero-stat"><div class="hero-stat-val">vs</div><div class="hero-stat-lbl">Buffett Comparison</div></div>
      <div class="hero-stat"><div class="hero-stat-val">∞</div><div class="hero-stat-lbl">Stocks</div></div>
    </div>
  </section>

  <section class="home-section" id="how-it-works">
    <div style="max-width:1100px;margin:0 auto;">
      <div class="section-label">Method</div>
      <h2 class="section-title">How Graham Signal works</h2>
      <p class="section-body">Enter a ticker symbol. Our AI applies Graham's rigorous analytical framework — ten criteria, three time horizons, and a direct comparison against Buffett's evolved modern approach.</p>
      <div class="hiw-grid">
        <div class="hiw-card"><div class="hiw-num">01</div><div class="hiw-title">Enter Your Ticker</div><div class="hiw-body">Provide a stock ticker symbol and your email to create or access your personal portfolio.</div></div>
        <div class="hiw-card"><div class="hiw-num">02</div><div class="hiw-title">Ten Criteria Applied</div><div class="hiw-body">Graham's ten defensive investor criteria are assessed — from margin of safety to current ratio, earnings stability, and the Graham Number.</div></div>
        <div class="hiw-card"><div class="hiw-num">03</div><div class="hiw-title">Graham vs Buffett</div><div class="hiw-body">See how the stock scores under Graham's strict quantitative framework versus Buffett's evolved qualitative approach.</div></div>
        <div class="hiw-card"><div class="hiw-num">04</div><div class="hiw-title">Portfolio CRM</div><div class="hiw-body">Save every analysis to your personal portfolio. Tag stocks as Watch, Bought, or Sold. Add private notes. Access anytime by email.</div></div>
      </div>
    </div>
  </section>

  <section class="home-section home-section-alt" id="criteria">
    <div style="max-width:1100px;margin:0 auto;">
      <div class="section-label">The Framework</div>
      <h2 class="section-title">Ten criteria. No shortcuts.</h2>
      <p class="section-body">Graham's method was empirical, patient, and unforgiving of speculation. Every criterion targets a specific dimension of financial safety and intrinsic value.</p>
      <div class="criteria-grid">
        <div class="criteria-card"><div class="criteria-num">Criterion 01</div><div class="criteria-name">Adequate Enterprise Size</div><div class="criteria-desc">Only large, established companies with sufficient scale and operational maturity are considered. Small caps carry disproportionate risk.</div></div>
        <div class="criteria-card"><div class="criteria-num">Criterion 02</div><div class="criteria-name">Financial Condition</div><div class="criteria-desc">Current assets must be at least twice current liabilities. Long-term debt must not exceed working capital.</div></div>
        <div class="criteria-card"><div class="criteria-num">Criterion 03</div><div class="criteria-name">Earnings Stability</div><div class="criteria-desc">Positive earnings reported in each of the past ten years without exception.</div></div>
        <div class="criteria-card"><div class="criteria-num">Criterion 04</div><div class="criteria-name">Dividend Record</div><div class="criteria-desc">Uninterrupted dividend payments for at least twenty years — consistency of shareholder returns.</div></div>
        <div class="criteria-card"><div class="criteria-num">Criterion 05</div><div class="criteria-name">Earnings Growth</div><div class="criteria-desc">Minimum one-third growth in EPS over the past decade, using three-year averages at each end.</div></div>
        <div class="criteria-card"><div class="criteria-num">Criterion 06</div><div class="criteria-name">Moderate P/E Ratio</div><div class="criteria-desc">Price-to-earnings ratio must not exceed 15 based on average earnings over three years.</div></div>
        <div class="criteria-card"><div class="criteria-num">Criterion 07</div><div class="criteria-name">Moderate Price-to-Book</div><div class="criteria-desc">P/B ratio ≤ 1.5. Combined P/E × P/B must not exceed 22.5.</div></div>
        <div class="criteria-card"><div class="criteria-num">Criterion 08</div><div class="criteria-name">Margin of Safety</div><div class="criteria-desc">The stock must trade at a meaningful discount to calculated intrinsic value — Graham's cornerstone principle.</div></div>
        <div class="criteria-card"><div class="criteria-num">Criterion 09</div><div class="criteria-name">Debt-to-Equity</div><div class="criteria-desc">Total debt must not exceed total equity. Graham was deeply cautious of leveraged capital structures.</div></div>
        <div class="criteria-card"><div class="criteria-num">Criterion 10</div><div class="criteria-name">Graham Number</div><div class="criteria-desc">√(22.5 × EPS × BVPS) — the fair value ceiling for the defensive investor.</div></div>
      </div>
    </div>
  </section>

  <section class="home-section" id="graham">
    <div class="graham-bio-grid">
      <div>
        <div class="section-label">The Man</div>
        <h2 class="section-title">Benjamin Graham,<br>1894–1976</h2>
        <div class="graham-pullquote">
          <blockquote>"The individual investor should act consistently as an investor and not as a speculator."</blockquote>
          <cite>— Benjamin Graham, The Intelligent Investor (1949)</cite>
        </div>
      </div>
      <div class="graham-bio-right section-body">
        <p>Benjamin Graham is widely considered the father of value investing. A professor at Columbia Business School and author of <em>Security Analysis</em> (1934) and <em>The Intelligent Investor</em> (1949), Graham developed a systematic, quantitative approach to assessing the intrinsic worth of securities.</p>
        <p>Where others speculated, Graham calculated. His framework demanded a <em>margin of safety</em> — buying significantly below intrinsic value to protect against error and misfortune. His principles prioritised financial soundness, earnings consistency, and strict valuation limits over narrative or growth projections.</p>
        <p>Warren Buffett, Graham's most celebrated student, called <em>The Intelligent Investor</em> "by far the best book about investing ever written." While Buffett later evolved toward qualitative factors — competitive moats, management quality, business durability — he has always credited Graham's framework as the foundation of his thinking.</p>
        <p>Graham Signal honours the original. It applies Graham's precise, academically rigorous criteria before offering a comparison against Buffett's evolved approach — illuminating where the two methods diverge.</p>
      </div>
    </div>
  </section>

  <section class="home-cta-section">
    <div style="position:relative;z-index:1;">
      <div class="section-label">Begin</div>
      <h2 class="section-title">Apply Graham's framework<br>to any stock, right now.</h2>
      <p>No speculation. No shortcuts. Rigorous, evidence-based value analysis built on decades of academic research.</p>
      <button class="btn-primary" onclick="enterApp()">Open Graham Signal →</button>
    </div>
  </section>

  <div class="home-disclaimer">
    <p><strong>Disclaimer:</strong> Graham Signal is an educational tool powered by AI. It does not constitute financial advice and should not be relied upon for investment decisions. All analysis is generated algorithmically based on publicly available information. Graham Signal is not affiliated with, endorsed by, or connected to the estate of Benjamin Graham, Columbia Business School, Berkshire Hathaway, or any associated institution. Always conduct independent due diligence before investing.</p>
  </div>

  <footer class="home-footer">
    <div class="footer-brand">Graham <em>Signal</em></div>
    <div class="footer-note">Applying the Intelligent Investor framework · Not affiliated with Benjamin Graham or his estate</div>
  </footer>

</div><!-- /#page-home -->


<!-- ══════════════ APP PAGE ══════════════ -->
<div id="page-app">

  <header>
    <div class="app-brand">
      <div class="app-logo">Graham <em>Signal</em></div>
      <div class="app-tag">Intelligent Investor Framework · v1.0</div>
    </div>
    <button class="btn-go-home" onclick="goHome()">← Home</button>
  </header>

  <div class="tabs">
    <button class="tab-btn active" id="tab-btn-analyse" onclick="switchTab('analyse',this)">🔬 Analyse a Stock</button>
    <button class="tab-btn" id="tab-btn-principles" onclick="switchTab('principles',this)">📖 The Criteria</button>
    <button class="tab-btn" id="tab-btn-portfolio" onclick="switchTab('portfolio',this)">📁 My Portfolio</button>
  </div>

  <!-- ── ANALYSE TAB ── -->
  <div id="tab-analyse" class="tab-content active">
    <div class="app-container">
      <div class="panel">
        <div class="panel-title">Stock Analysis</div>
        <div class="form-row">
          <div class="form-group" style="flex:0 0 160px;">
            <label>Ticker Symbol *</label>
            <input type="text" id="inp-ticker" placeholder="e.g. KO" maxlength="12">
          </div>
          <div class="form-group">
            <label>Company Name (optional)</label>
            <input type="text" id="inp-company" placeholder="e.g. The Coca-Cola Company">
          </div>
          <div class="form-group" style="flex:0 0 240px;">
            <label>Your Email (for portfolio)</label>
            <input type="email" id="inp-email" placeholder="you@example.com">
          </div>
        </div>
        <button class="btn-analyse" onclick="analyseStock()" id="btn-analyse">
          <span>Run Graham Analysis</span><span>→</span>
        </button>
        <div class="error-msg" id="err-msg"></div>
      </div>

      <div class="loading-state" id="loading-state">
        <div class="loading-spinner"></div>
        <p>Applying Graham's ten criteria to this stock…</p>
      </div>

      <!-- RESULT -->
      <div id="result-panel" class="result-panel">

        <div class="panel">
          <div class="result-header-grid">
            <div class="score-circle">
              <div class="score-val" id="r-score">—</div>
              <div class="score-lbl">Graham Score</div>
            </div>
            <div class="result-meta">
              <div class="result-ticker" id="r-ticker"></div>
              <div class="result-company" id="r-company"></div>
              <div class="verdict-badge" id="r-badge"></div>
              <div class="result-date" id="r-date"></div>
            </div>
          </div>

          <div id="tagging-section" style="display:none;">
            <div class="section-divider"></div>
            <div class="panel-title">Portfolio Tag</div>
            <div class="tag-row">
              <button class="stock-tag watch"  id="tag-watch"  onclick="setTag('watch')">👁 Watchlist</button>
              <button class="stock-tag bought" id="tag-bought" onclick="setTag('bought')">✅ Bought</button>
              <button class="stock-tag sold"   id="tag-sold"   onclick="setTag('sold')">📤 Sold</button>
            </div>
            <textarea class="notes-area" id="notes-area" placeholder="Add private notes about this stock…"></textarea>
            <button class="btn-save-notes" onclick="saveNotesAndTag()">Save to Portfolio</button>
            <div class="success-msg" id="notes-saved-msg">✓ Saved to your portfolio</div>
          </div>
        </div>

        <div class="panel">
          <div class="panel-title">Ten Criteria Assessment</div>
          <div class="criteria-scores-grid" id="criteria-scores-grid"></div>
        </div>

        <div class="panel">
          <div class="panel-title">Time Horizon Outlook</div>
          <div class="horizons-row" id="horizons-row"></div>
        </div>

        <div class="panel">
          <div class="panel-title">Graham vs Buffett — Framework Comparison</div>
          <div class="buffett-comparison">
            <div class="comparison-scores">
              <div class="comp-score-block">
                <div class="comp-score-val gs" id="comp-gs-score">—</div>
                <div class="comp-score-name">Graham Signal Score</div>
              </div>
              <div class="comp-divider"></div>
              <div class="comp-score-block">
                <div class="comp-score-val buf" id="comp-buf-score">—</div>
                <div class="comp-score-name">Est. Buffett Score</div>
              </div>
            </div>
            <div class="comparison-summary" id="comp-summary"></div>
          </div>
        </div>

        <div class="panel">
          <div class="panel-title">Analytical Verdict</div>
          <div class="narrative-text" id="r-narrative"></div>
          <div class="graham-quote-box" id="r-quote-box" style="display:none;">
            <div class="graham-quote-text" id="r-quote"></div>
            <div class="graham-quote-source">— Benjamin Graham</div>
          </div>
        </div>

      </div><!-- /#result-panel -->
    </div>
  </div>

  <!-- ── PRINCIPLES TAB ── -->
  <div id="tab-principles" class="tab-content">
    <div class="app-container">
      <div class="principles-intro">
        <p>Benjamin Graham's ten criteria for the <em>Defensive Investor</em> were designed to eliminate speculation and ensure a margin of safety. First codified in <em>The Intelligent Investor</em> (1949) and expanded in <em>Security Analysis</em>, these criteria remain among the most rigorous quantitative screens in value investing. Each criterion is explained with its original formulation, its analytical purpose, and notes on how it applies to modern markets.</p>
      </div>
      <div class="principles-list">

        <div class="guide-principle">
          <div class="guide-num">Criterion 01</div>
          <div class="guide-title">Adequate Enterprise Size</div>
          <div class="guide-body">Graham insisted on investing only in companies of sufficient scale. Smaller companies carry disproportionate operational risk, are more susceptible to competitive disruption, and lack the institutional resilience of larger enterprises. For industrial companies, Graham required revenues of at least $100 million; for utilities, assets of at least $50 million — thresholds that translate to considerably higher figures in today's currency.</div>
          <ul class="guide-bullets">
            <li>Large-cap companies demonstrate operational maturity and market durability</li>
            <li>Scale provides negotiating leverage, diversification, and access to capital markets</li>
            <li>Small companies lack the financial cushion needed during economic downturns</li>
          </ul>
          <div class="guide-modern-note"><strong>Modern application:</strong> Analysts typically use a minimum market capitalisation of $2–5 billion as a contemporary proxy for Graham's size requirement, adjusted for inflation and expanded global markets.</div>
        </div>

        <div class="guide-principle">
          <div class="guide-num">Criterion 02</div>
          <div class="guide-title">Financial Condition — Current Ratio</div>
          <div class="guide-body">Graham's most quantitatively precise criterion: current assets must be at least twice current liabilities (current ratio ≥ 2.0). Additionally, long-term debt should not exceed net current assets (working capital). This dual test ensures both short-term liquidity and that the company is not over-leveraged relative to its liquid resources.</div>
          <ul class="guide-bullets">
            <li>Current ratio ≥ 2.0 indicates the company can comfortably meet short-term obligations</li>
            <li>Long-term debt ≤ working capital prevents hidden leverage distorting the balance sheet</li>
            <li>This criterion by design excludes most financial companies and utilities</li>
          </ul>
          <div class="guide-modern-note"><strong>Modern application:</strong> Many analysts accept a current ratio of ≥ 1.5 for asset-light businesses. For technology companies, the long-term debt constraint is often interpreted relative to EBITDA rather than working capital.</div>
        </div>

        <div class="guide-principle">
          <div class="guide-num">Criterion 03</div>
          <div class="guide-title">Earnings Stability</div>
          <div class="guide-body">The company must have reported positive net earnings in each of the past ten years without exception. Graham viewed earnings stability as a proxy for business quality and competitive durability. A single year of losses in a decade was grounds for exclusion from the defensive portfolio.</div>
          <ul class="guide-bullets">
            <li>Ten consecutive profitable years demonstrates resilience through multiple economic cycles</li>
            <li>A single loss year reveals vulnerability to external or internal disruption</li>
            <li>Consistent profitability signals management competence and durable demand</li>
          </ul>
          <div class="guide-modern-note"><strong>Modern application:</strong> Analysts may adjust for extraordinary items — asset write-downs or pandemic-period disruptions — that obscure underlying operational profitability without reflecting a structural weakness.</div>
        </div>

        <div class="guide-principle">
          <div class="guide-num">Criterion 04</div>
          <div class="guide-title">Dividend Record</div>
          <div class="guide-body">Uninterrupted dividend payments for a minimum of twenty years. Graham placed enormous weight on dividend consistency because it demonstrated both financial strength and management discipline. Cutting a dividend signals distress; maintaining one through full economic cycles signals genuine durability.</div>
          <ul class="guide-bullets">
            <li>A twenty-year track record spans at least two complete economic cycles</li>
            <li>Dividend payments provide measurable shareholder return independent of market price</li>
            <li>Consistent dividends constrain management's ability to misallocate capital</li>
          </ul>
          <div class="guide-modern-note"><strong>Modern application:</strong> Many high-quality growth companies (Amazon, Alphabet) pay no dividends but create substantial value. Analysts often substitute share buyback consistency as a partial proxy for mature businesses that do not pay dividends.</div>
        </div>

        <div class="guide-principle">
          <div class="guide-num">Criterion 05</div>
          <div class="guide-title">Earnings Growth</div>
          <div class="guide-body">Earnings per share must have increased by at least one-third over the past ten years, using three-year averages at each end to avoid distortion from a single exceptional year. This requirement ensures the company's earnings power is genuinely expanding — growth, however modest, is demanded.</div>
          <ul class="guide-bullets">
            <li>One-third growth over a decade equates to approximately 3% compound annual growth — a modest but meaningful bar</li>
            <li>Three-year averaging prevents manipulation or distortion from cyclical peaks and troughs</li>
            <li>Earnings growth confirms the business is genuinely expanding its productive capacity</li>
          </ul>
          <div class="guide-modern-note"><strong>Modern application:</strong> Many analysts increase the required growth rate to 5–7% CAGR to account for inflation and competitive intensity in today's markets.</div>
        </div>

        <div class="guide-principle">
          <div class="guide-num">Criterion 06</div>
          <div class="guide-title">Moderate P/E Ratio</div>
          <div class="guide-body">The price-to-earnings ratio must not exceed 15 times average earnings of the past three years. This is one of Graham's most cited — and most debated — criteria. It reflects his deep scepticism of paying a premium for future growth that may never materialise. The P/E ceiling anchors analysis to current earning power, not projections.</div>
          <ul class="guide-bullets">
            <li>P/E ≤ 15 reflects fair value; P/E above 25 reflects speculation, not investment</li>
            <li>Three-year average earnings smooth cyclical distortions and one-off gains</li>
            <li>High P/E ratios require future growth assumptions that introduce forecasting risk</li>
          </ul>
          <div class="guide-modern-note"><strong>Modern application:</strong> In low-interest-rate environments, market P/E ratios have expanded well beyond Graham's threshold. Analysts use cyclically adjusted P/E (CAPE) and relative sector P/E as more contextual measures. Graham's P/E ≤ 15 is treated as a strict floor for value stocks.</div>
        </div>

        <div class="guide-principle">
          <div class="guide-num">Criterion 07</div>
          <div class="guide-title">Moderate Price-to-Book Ratio</div>
          <div class="guide-body">The price-to-book value ratio must not exceed 1.5. Furthermore, the product of P/E multiplied by P/B must not exceed 22.5. This combined test — the Graham Number condition — prevents a low P/B from masking an excessive P/E, or vice versa.</div>
          <ul class="guide-bullets">
            <li>P/B ≤ 1.5 suggests the market is not paying an excessive premium over tangible assets</li>
            <li>P/E × P/B ≤ 22.5 combines both valuation metrics into a single composite ceiling</li>
            <li>This criterion links directly to the Graham Number formula: √(22.5 × EPS × BVPS)</li>
          </ul>
          <div class="guide-modern-note"><strong>Modern application:</strong> Book value is increasingly unreliable for knowledge-economy companies where intangible assets dominate. Price-to-tangible-book and EV/EBITDA are widely used as supplements for asset-heavy industries.</div>
        </div>

        <div class="guide-principle">
          <div class="guide-num">Criterion 08</div>
          <div class="guide-title">Margin of Safety</div>
          <div class="guide-body">The cornerstone of Graham's entire philosophy. The margin of safety is the gap between a stock's intrinsic value and its current market price. Graham demanded purchasing at a significant discount — typically 33–50% below intrinsic value — as protection against errors of estimation, adverse developments, and market irrationality. Without a margin of safety, even a good company becomes a speculative investment.</div>
          <ul class="guide-bullets">
            <li>Margin of safety is the primary defence against the inherent uncertainty of forecasting</li>
            <li>A 33–50% discount means the analyst can be substantially wrong and still not lose money</li>
            <li>Intrinsic value is calculated primarily from current assets, earnings, and the Graham Number</li>
          </ul>
          <div class="guide-modern-note"><strong>Modern application:</strong> This principle remains universally accepted. Buffett, Munger, Klarman, and virtually all value investors have built their approach around it. The required margin of safety scales with uncertainty — technology companies demand a larger cushion than regulated utilities.</div>
        </div>

        <div class="guide-principle">
          <div class="guide-num">Criterion 09</div>
          <div class="guide-title">Debt-to-Equity Ratio</div>
          <div class="guide-body">Total debt must not exceed total equity — a debt-to-equity ratio of 1.0 or below. Graham was deeply cautious of leverage, viewing it as an amplifier of both risk and potential error. High debt obligations can transform a temporary business difficulty into a permanent solvency crisis.</div>
          <ul class="guide-bullets">
            <li>D/E ≤ 1.0 ensures equity holders are not subordinated to excessive creditor claims</li>
            <li>Low leverage provides resilience during economic contractions and credit tightening</li>
            <li>Graham viewed debt as a permanent constraint on management's flexibility and judgement</li>
          </ul>
          <div class="guide-modern-note"><strong>Modern application:</strong> Many high-quality consumer staples and pharmaceutical businesses carry conservative debt loads that satisfy Graham's criterion. Capital-intensive industries are typically evaluated using interest coverage ratios and net debt/EBITDA.</div>
        </div>

        <div class="guide-principle">
          <div class="guide-num">Criterion 10</div>
          <div class="guide-title">The Graham Number</div>
          <div class="guide-body">The Graham Number provides a formula for the maximum price a defensive investor should pay: √(22.5 × Earnings Per Share × Book Value Per Share). It derives directly from the combined P/E × P/B ceiling of 22.5. A current stock price below the Graham Number merits further investigation; significantly above, it fails the test regardless of other merits.</div>
          <ul class="guide-bullets">
            <li>Combines P/E and P/B constraints into a single fair-value price ceiling</li>
            <li>Stocks trading below their Graham Number offer a quantitative margin of safety</li>
            <li>Most reliable for stable, capital-intensive businesses with tangible, measurable assets</li>
          </ul>
          <div class="guide-modern-note"><strong>Modern application:</strong> The Graham Number is widely used as an initial screen, not a definitive valuation. For companies with negative book value or high intangibles, the formula breaks down. Analysts combine it with DCF, EV/EBITDA, and sector-relative analysis for a complete picture.</div>
        </div>

      </div><!-- /.principles-list -->
    </div>
  </div>

  <!-- ── PORTFOLIO TAB ── -->
  <div id="tab-portfolio" class="tab-content">
    <div class="app-container">

      <div id="portfolio-login-section">
        <div class="portfolio-login-wrap">
          <h2>Your Portfolio</h2>
          <p>Enter your email address to access your personal portfolio of Graham Signal analyses. Your email is your profile identifier — no password required.</p>
          <div class="login-row">
            <input type="email" id="portfolio-email-inp" placeholder="your@email.com" onkeydown="if(event.key==='Enter')loadPortfolio()">
            <button class="btn-login" onclick="loadPortfolio()">Access →</button>
          </div>
          <div class="error-msg" id="portfolio-err" style="max-width:420px;margin:16px auto 0;"></div>
        </div>
      </div>

      <div id="portfolio-view">
        <div class="portfolio-header">
          <div class="portfolio-identity">
            <h3 id="portfolio-email-display"></h3>
            <p>Your Graham Signal portfolio</p>
          </div>
          <div style="display:flex;gap:10px;align-items:center;flex-wrap:wrap;">
            <button class="btn-refresh-all" id="btn-refresh-all" onclick="refreshAllPrices()">⟳ Refresh All Prices</button>
            <span class="refresh-all-status" id="refresh-all-status"></span>
            <button class="btn-logout" onclick="logoutPortfolio()">Switch Account</button>
          </div>
        </div>

        <div class="portfolio-stats" id="portfolio-stats"></div>

        <div class="portfolio-filters">
          <button class="filter-btn active" onclick="filterPortfolio('all',this)">All</button>
          <button class="filter-btn" onclick="filterPortfolio('watch',this)">👁 Watchlist</button>
          <button class="filter-btn" onclick="filterPortfolio('bought',this)">✅ Bought</button>
          <button class="filter-btn" onclick="filterPortfolio('sold',this)">📤 Sold</button>
          <button class="filter-btn" onclick="filterPortfolio('untagged',this)">Untagged</button>
          <button class="filter-btn" onclick="sortPortfolio('score',this)">Score ↓</button>
          <button class="filter-btn" onclick="sortPortfolio('date',this)">Recent</button>
        </div>

        <div class="portfolio-list" id="portfolio-list"></div>
      </div>

    </div>
  </div>

</div><!-- /#page-app -->

<script>
// ═══════════════════════════════════
//  STATE
// ═══════════════════════════════════
let _currentAnalysis = null;
let _currentEmail    = null;
let _portfolioCache  = [];
let _currentTag      = null;
let _portfolioFilter = 'all';
let _portfolioSort   = 'date';

// ═══════════════════════════════════
//  NAV
// ═══════════════════════════════════
function smoothScroll(id) {
  event.preventDefault();
  const el = document.getElementById(id);
  if (el) el.scrollIntoView({behavior:'smooth'});
}
function enterApp() {
  document.getElementById('page-home').style.display = 'none';
  document.getElementById('page-app').style.display  = 'block';
  window.scrollTo(0,0);
}
function goHome() {
  document.getElementById('page-app').style.display  = 'none';
  document.getElementById('page-home').style.display = 'block';
  window.scrollTo(0,0);
}
function switchTab(name, btn) {
  document.querySelectorAll('.tab-content').forEach(t => t.classList.remove('active'));
  document.querySelectorAll('.tab-btn').forEach(b => b.classList.remove('active'));
  document.getElementById('tab-' + name).classList.add('active');
  btn.classList.add('active');
  if (name === 'portfolio' && _currentEmail) renderPortfolioView(_currentEmail);
}

// ═══════════════════════════════════
//  UTILITY
// ═══════════════════════════════════
function esc(s) {
  if (s === null || s === undefined) return '';
  return String(s)
    .replace(/&/g,'&amp;').replace(/</g,'&lt;')
    .replace(/>/g,'&gt;').replace(/"/g,'&quot;');
}
function showErr(el, msg) { el.textContent = msg; el.style.display = 'block'; }

// ═══════════════════════════════════
//  ANALYSE
// ═══════════════════════════════════
async function analyseStock() {
  const ticker  = (document.getElementById('inp-ticker').value||'').trim().toUpperCase();
  const company = (document.getElementById('inp-company').value||'').trim();
  const email   = (document.getElementById('inp-email').value||'').trim();
  const errEl   = document.getElementById('err-msg');
  errEl.style.display = 'none';

  if (!ticker) { showErr(errEl, 'Please enter a ticker symbol.'); return; }

  _currentEmail = email || null;
  _currentTag   = null;

  const btn = document.getElementById('btn-analyse');
  btn.disabled = true;
  document.getElementById('loading-state').style.display = 'block';
  document.getElementById('result-panel').style.display  = 'none';

  try {
    const result = await callGrahamAPI(ticker, company);
    result.ticker  = ticker;
    result.company = result.company_name || company || ticker;
    result.date    = new Date().toLocaleDateString('en-GB', {day:'numeric',month:'long',year:'numeric'});
    result.email   = email || null;
    _currentAnalysis = result;
    if (email) await saveAnalysis(email, result);
    renderResult(result);
  } catch(e) {
    showErr(errEl, 'Analysis failed: ' + e.message);
  } finally {
    btn.disabled = false;
    document.getElementById('loading-state').style.display = 'none';
  }
}

// ═══════════════════════════════════
//  API
// ═══════════════════════════════════
async function callGrahamAPI(ticker, company) {
  const prompt = `You are Graham Signal — an AI that applies Benjamin Graham's ten defensive investor criteria to analyse stocks with academic rigour and precision. Graham was a professor at Columbia Business School; your tone is measured, evidence-based, analytical, and entirely free of speculation or sentiment.

Analyse the stock: ${ticker}${company ? ' (' + company + ')' : ''}.

Return ONLY a valid JSON object — no markdown, no preamble, no trailing text — with this exact structure:

{
  "company_name": "Full legal company name",
  "overall_score": <integer 0-100, Graham's overall assessment — be strict, most stocks will score below 50>,
  "buffett_score": <integer 0-100, estimated score under Buffett's evolved framework which values moats, management quality, and durable competitive advantage over pure price metrics>,
  "verdict_level": <"STRONG BUY" | "BUY" | "HOLD" | "SELL" | "AVOID">,
  "verdict": "One precise sentence summarising the verdict",
  "criteria": [
    {
      "name": "Criterion name (exact)",
      "score": <integer 0-10, score strictly: 8-10 = criterion clearly satisfied, 5-7 = partially satisfied, 0-4 = criterion not satisfied>,
      "comment": "2-3 factual sentences assessing this stock specifically against this criterion. Reference actual financial metrics where possible."
    }
  ],
  "horizons": [
    {"label":"1 Year","rating":"One-word or short rating e.g. Cautious / Neutral / Constructive","stars":<1-5>,"note":"One sentence outlook for this timeframe"},
    {"label":"5 Years","rating":"...","stars":<1-5>,"note":"..."},
    {"label":"10 Years","rating":"...","stars":<1-5>,"note":"..."}
  ],
  "comparison_summary": "2-3 sentences comparing Graham's framework score to Buffett's estimated score. Be specific: explain where the two frameworks diverge for this particular stock — e.g. Buffett would value the moat more, Graham penalises the P/E, etc.",
  "narrative": "3-4 sentences in Graham's academic, measured, unsentimental voice delivering a verdict on this stock. Reference specific criteria by name. Do not use first person. Do not be theatrical.",
  "graham_quote": "An accurate, real quotation from Benjamin Graham that is specifically relevant to this stock's situation"
}

The criteria array must contain exactly 10 items in this order:
1. Adequate Enterprise Size
2. Financial Condition (Current Ratio ≥ 2.0)
3. Earnings Stability (10 consecutive profitable years)
4. Dividend Record (20+ years uninterrupted)
5. Earnings Growth (≥ 33% over 10 years)
6. Moderate P/E Ratio (≤ 15 based on 3-year avg earnings)
7. Moderate Price-to-Book (P/B ≤ 1.5; P/E × P/B ≤ 22.5)
8. Margin of Safety vs Intrinsic Value
9. Debt-to-Equity Ratio (≤ 1.0)
10. Graham Number Valuation (stock price vs √(22.5 × EPS × BVPS))

Score conservatively. Return ONLY the raw JSON.`;

  const resp = await fetch('https://api.anthropic.com/v1/messages', {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({
      model: 'claude-sonnet-4-20250514',
      max_tokens: 3000,
      messages: [{ role: 'user', content: prompt }]
    })
  });

  if (!resp.ok) {
    const txt = await resp.text();
    throw new Error('HTTP ' + resp.status + ': ' + txt.slice(0,140));
  }

  const data = await resp.json();
  const text = (data.content || []).map(b => b.text || '').join('');
  const start = text.indexOf('{');
  const end   = text.lastIndexOf('}');
  if (start === -1 || end === -1) throw new Error('No valid JSON found in response. Please try again.');
  return JSON.parse(text.slice(start, end + 1));
}

// ═══════════════════════════════════
//  RENDER RESULT
// ═══════════════════════════════════
function renderResult(r) {
  // Score
  const scoreEl = document.getElementById('r-score');
  scoreEl.textContent = r.overall_score;
  const sc = r.overall_score || 0;
  if (sc >= 65) scoreEl.style.color = '#2a6b6b';
  else if (sc >= 45) scoreEl.style.color = '#b8860b';
  else scoreEl.style.color = '#8b1a1a';

  document.getElementById('r-ticker').textContent  = r.ticker;
  document.getElementById('r-company').textContent = r.company;
  document.getElementById('r-date').textContent    = r.date;

  const badge = document.getElementById('r-badge');
  badge.textContent = r.verdict_level;
  badge.className   = 'verdict-badge verdict-' + (r.verdict_level||'HOLD').replace(/\s+/g,'-');

  // Tagging section
  const tagSec = document.getElementById('tagging-section');
  if (r.email) {
    tagSec.style.display = 'block';
    // Load existing tag/notes if already in portfolio
    const existing = getStorageRecords(r.email).find(x => x.ticker === r.ticker);
    _currentTag = existing ? (existing.tag || null) : null;
    document.getElementById('notes-area').value = existing ? (existing.notes || '') : '';
    updateTagButtons(_currentTag);
    document.getElementById('notes-saved-msg').style.display = 'none';
  } else {
    tagSec.style.display = 'none';
  }

  // Criteria
  const grid = document.getElementById('criteria-scores-grid');
  grid.innerHTML = (r.criteria || []).map(c => {
    const pct = ((c.score||0) / 10) * 100;
    const col = c.score >= 7 ? '#2a6b6b' : c.score >= 4 ? '#b8860b' : '#8b1a1a';
    return `<div class="criterion-card">
      <div class="criterion-header">
        <div class="criterion-name">${esc(c.name)}</div>
        <div class="criterion-score">${c.score}/10</div>
      </div>
      <div class="criterion-bar"><div class="criterion-fill" style="width:${pct}%;background:${col};"></div></div>
      <div class="criterion-comment">${esc(c.comment)}</div>
    </div>`;
  }).join('');

  // Horizons
  document.getElementById('horizons-row').innerHTML = (r.horizons || []).map(h => {
    const s = h.stars || 0;
    return `<div class="horizon-card">
      <div class="horizon-label">${esc(h.label)}</div>
      <div class="horizon-rating">${esc(h.rating)}</div>
      <div class="horizon-stars">${'★'.repeat(s)}${'☆'.repeat(5-s)}</div>
      <div class="horizon-note">${esc(h.note)}</div>
    </div>`;
  }).join('');

  // Comparison
  document.getElementById('comp-gs-score').textContent  = r.overall_score;
  document.getElementById('comp-buf-score').textContent = r.buffett_score || '—';
  document.getElementById('comp-summary').textContent   = r.comparison_summary || '';

  // Narrative
  document.getElementById('r-narrative').textContent = r.narrative || '';
  const qbox = document.getElementById('r-quote-box');
  if (r.graham_quote) {
    document.getElementById('r-quote').textContent = '\u201c' + r.graham_quote + '\u201d';
    qbox.style.display = 'block';
  } else {
    qbox.style.display = 'none';
  }

  document.getElementById('result-panel').style.display = 'block';
  setTimeout(() => document.getElementById('result-panel').scrollIntoView({behavior:'smooth', block:'start'}), 50);
}

// ═══════════════════════════════════
//  TAGS & NOTES
// ═══════════════════════════════════
function setTag(tag) {
  _currentTag = (_currentTag === tag) ? null : tag;
  updateTagButtons(_currentTag);
}
function updateTagButtons(active) {
  ['watch','bought','sold'].forEach(t => {
    const btn = document.getElementById('tag-' + t);
    if (btn) btn.classList.toggle('active', t === active);
  });
}
async function saveNotesAndTag() {
  if (!_currentAnalysis || !_currentAnalysis.email) return;
  _currentAnalysis.tag   = _currentTag;
  _currentAnalysis.notes = document.getElementById('notes-area').value;
  await saveAnalysis(_currentAnalysis.email, _currentAnalysis);
  const msg = document.getElementById('notes-saved-msg');
  msg.style.display = 'block';
  setTimeout(() => msg.style.display = 'none', 2500);
}

// ═══════════════════════════════════
//  STORAGE — Firebase + localStorage cache
// ═══════════════════════════════════
const STORAGE_KEY = 'graham_signal_v1'; // kept for local cache

function _lsGetRecords(email) {
  try {
    const all = JSON.parse(localStorage.getItem(STORAGE_KEY) || '{}');
    return all[email.toLowerCase()] || [];
  } catch { return []; }
}
function _lsSetRecords(email, records) {
  try {
    const all = JSON.parse(localStorage.getItem(STORAGE_KEY) || '{}');
    all[email.toLowerCase()] = records;
    localStorage.setItem(STORAGE_KEY, JSON.stringify(all));
  } catch(e) { console.warn('localStorage write failed', e); }
}

// Show a subtle sync indicator
function _syncStatus(msg, isErr) {
  let el = document.getElementById('_sync-status');
  if (!el) {
    el = document.createElement('div');
    el.id = '_sync-status';
    el.style.cssText = 'position:fixed;bottom:16px;right:16px;z-index:9999;font-size:0.72rem;padding:6px 14px;border-radius:20px;font-family:DM Sans,sans-serif;transition:opacity 0.4s;pointer-events:none;';
    document.body.appendChild(el);
  }
  el.style.background  = isErr ? 'rgba(139,26,26,0.9)' : 'rgba(42,107,107,0.9)';
  el.style.color       = '#fff';
  el.style.opacity     = '1';
  el.textContent       = msg;
  clearTimeout(el._t);
  el._t = setTimeout(() => el.style.opacity = '0', 2800);
}

// Unified load — Firebase first, localStorage fallback
async function getStorageRecords(email) {
  const fb = window._fb;
  if (fb) {
    const records = await fb.load(email);
    if (records !== null) {
      _lsSetRecords(email, records); // update cache
      return records;
    }
  }
  // Offline fallback
  return _lsGetRecords(email);
}

// Unified save — write to localStorage immediately, Firebase async
async function saveAnalysis(email, analysis) {
  if (!email) return;
  // Build updated records list
  let records = _lsGetRecords(email).filter(x => x.ticker !== analysis.ticker);
  const record = {
    ticker: analysis.ticker, company: analysis.company, date: analysis.date,
    overall_score: analysis.overall_score, buffett_score: analysis.buffett_score,
    verdict_level: analysis.verdict_level, verdict: analysis.verdict,
    criteria: analysis.criteria, horizons: analysis.horizons,
    comparison_summary: analysis.comparison_summary,
    narrative: analysis.narrative, graham_quote: analysis.graham_quote,
    tag: analysis.tag || null, notes: analysis.notes || '',
    lots: analysis.lots || [],
    currentPrice: analysis.currentPrice || null,
    pricesAsOf: analysis.pricesAsOf || null,
    email: email
  };
  records.unshift(record);
  records = records.slice(0, 100);

  // Instant local save
  _lsSetRecords(email, records);

  // Async Firebase save
  const fb = window._fb;
  if (fb) {
    fb.save(email, records).then(ok => {
      if (ok) _syncStatus('✓ Saved to cloud');
      else    _syncStatus('⚠ Cloud sync failed — saved locally', true);
    });
  }
}

// Delete an account (used by admin + user)
async function deleteAccountData(email) {
  // Remove from localStorage
  try {
    const all = JSON.parse(localStorage.getItem(STORAGE_KEY) || '{}');
    delete all[email.toLowerCase()];
    localStorage.setItem(STORAGE_KEY, JSON.stringify(all));
  } catch(e) {}
  // Remove from Firebase
  const fb = window._fb;
  if (fb) await fb.remove(email);
}

// ═══════════════════════════════════
//  PORTFOLIO
// ═══════════════════════════════════
async function loadPortfolio() {
  const email = (document.getElementById('portfolio-email-inp').value||'').trim().toLowerCase();
  const errEl = document.getElementById('portfolio-err');
  errEl.style.display = 'none';
  if (!email || !email.includes('@')) {
    showErr(errEl, 'Please enter a valid email address.'); return;
  }

  // Show loading state
  const btn = document.querySelector('.btn-login');
  if (btn) { btn.textContent = 'Loading…'; btn.disabled = true; }

  _currentEmail = email;

  try {
    await renderPortfolioView(email);
  } finally {
    if (btn) { btn.textContent = 'Access →'; btn.disabled = false; }
  }
}

async function renderPortfolioView(email) {
  document.getElementById('portfolio-login-section').style.display = 'none';
  document.getElementById('portfolio-view').style.display = 'block';
  document.getElementById('portfolio-email-display').textContent = email;

  // Show skeleton while loading
  document.getElementById('portfolio-list').innerHTML =
    '<div class="empty-portfolio"><p style="color:var(--muted)">Loading portfolio from cloud…</p></div>';

  const records = await getStorageRecords(email);
  _portfolioCache = records;
  renderPortfolioStats(records);
  renderPortfolioList(records);
}

function renderPortfolioStats(records) {
  const total = records.length;
  const avgScore = total > 0
    ? Math.round(records.reduce((s,r) => s + (r.overall_score||0), 0) / total)
    : '—';
  document.getElementById('portfolio-stats').innerHTML = `
    <div class="stat-card"><div class="stat-val">${total}</div><div class="stat-lbl">Total Stocks</div></div>
    <div class="stat-card"><div class="stat-val">${avgScore}</div><div class="stat-lbl">Avg Score</div></div>
    <div class="stat-card"><div class="stat-val">${records.filter(r=>r.tag==='watch').length}</div><div class="stat-lbl">Watchlist</div></div>
    <div class="stat-card"><div class="stat-val">${records.filter(r=>r.tag==='bought').length}</div><div class="stat-lbl">Bought</div></div>
    <div class="stat-card"><div class="stat-val">${records.filter(r=>r.tag==='sold').length}</div><div class="stat-lbl">Sold</div></div>
  `;
}

function filterPortfolio(filter, btn) {
  _portfolioFilter = filter;
  document.querySelectorAll('.portfolio-filters .filter-btn').forEach(b => b.classList.remove('active'));
  if (btn) btn.classList.add('active');
  renderPortfolioList(_portfolioCache);
}

function sortPortfolio(by, btn) {
  _portfolioSort = by;
  renderPortfolioList(_portfolioCache);
}

function renderPortfolioList(records) {
  let filtered = [...records];
  if (_portfolioFilter === 'watch')     filtered = filtered.filter(r => r.tag === 'watch');
  else if (_portfolioFilter === 'bought')  filtered = filtered.filter(r => r.tag === 'bought');
  else if (_portfolioFilter === 'sold')    filtered = filtered.filter(r => r.tag === 'sold');
  else if (_portfolioFilter === 'untagged') filtered = filtered.filter(r => !r.tag);
  if (_portfolioSort === 'score') filtered.sort((a,b) => (b.overall_score||0) - (a.overall_score||0));

  const el = document.getElementById('portfolio-list');
  if (filtered.length === 0) {
    el.innerHTML = '<div class="empty-portfolio"><p>No analyses found. Run an analysis and save it to your portfolio.</p></div>';
    return;
  }

  el.innerHTML = filtered.map(r => {
    const idx      = _portfolioCache.indexOf(r);
    const tagBadge = r.tag ? `<span class="pi-tag-badge pi-tag-${esc(r.tag)}">${r.tag==='watch'?'👁 Watch':r.tag==='bought'?'✅ Bought':'📤 Sold'}</span>` : '';
    const notesBadge = r.notes ? '<span>📝 Notes</span>' : '';
    const lots     = r.lots || [];
    const lotCount = lots.length;
    const holdingsSummary = lotCount > 0
      ? `${lotCount} lot${lotCount>1?'s':''} · ${lots.reduce((s,l)=>s+(parseFloat(l.units)||0),0)} units`
      : 'No lots — click to add';

    return `
    <div class="portfolio-item" style="display:block;cursor:default;padding:0;" id="pi-${idx}">
      <div style="display:grid;grid-template-columns:1fr auto;gap:12px;align-items:center;padding:18px 22px;cursor:pointer;" onclick="restoreFromPortfolio(${idx})">
        <div>
          <div class="pi-ticker">${esc(r.ticker)}</div>
          <div class="pi-company">${esc(r.company)}</div>
          <div class="pi-meta">${tagBadge}<span>${esc(r.date)}</span>${notesBadge}</div>
        </div>
        <div>
          <div class="pi-score">${r.overall_score}</div>
          <div class="pi-verdict">${esc(r.verdict_level)}</div>
        </div>
      </div>
      <div class="holdings-section">
        <button class="holdings-toggle" id="ht-btn-${idx}" onclick="toggleHoldings(${idx}, event)">
          <span>📊 Holdings</span>
          <span style="display:flex;align-items:center;gap:10px;">
            <span class="ht-summary" id="ht-summary-${idx}">${holdingsSummary}</span>
            <span class="ht-arrow">▼</span>
          </span>
        </button>
        <div class="holdings-body" id="hb-${idx}">
          <div class="holdings-actions">
            <button class="btn-add-lot" onclick="openAddLot(${idx}, event)">＋ Add Purchase Lot</button>
            <div style="display:flex;align-items:center;gap:10px;">
              <span class="price-as-of" id="price-as-of-${idx}">${r.pricesAsOf ? 'Prices as of ' + r.pricesAsOf : ''}</span>
              <button class="btn-refresh-price" id="rfbtn-${idx}" onclick="refreshOneLot(${idx}, event)">⟳ Refresh Price</button>
            </div>
          </div>
          <div class="add-lot-form" id="alf-${idx}">
            <div class="lot-form-row">
              <div class="lot-form-group" style="flex:0 0 130px;">
                <label>Purchase Date</label>
                <input type="date" id="lf-date-${idx}">
              </div>
              <div class="lot-form-group" style="flex:0 0 110px;">
                <label>Currency</label>
                <select id="lf-cur-${idx}">
                  <option value="USD">USD $</option>
                  <option value="GBP">GBP £</option>
                  <option value="EUR">EUR €</option>
                  <option value="JPY">JPY ¥</option>
                  <option value="CAD">CAD $</option>
                  <option value="AUD">AUD $</option>
                  <option value="CHF">CHF</option>
                  <option value="HKD">HKD</option>
                  <option value="SEK">SEK</option>
                  <option value="NOK">NOK</option>
                  <option value="DKK">DKK</option>
                  <option value="INR">INR ₹</option>
                </select>
              </div>
              <div class="lot-form-group">
                <label>Price Paid (per unit)</label>
                <input type="number" id="lf-price-${idx}" placeholder="e.g. 142.50" min="0" step="0.01">
              </div>
              <div class="lot-form-group" style="flex:0 0 110px;">
                <label>Units</label>
                <input type="number" id="lf-units-${idx}" placeholder="e.g. 50" min="0" step="0.001">
              </div>
            </div>
            <div class="lot-form-actions">
              <button class="btn-lot-save" onclick="saveLot(${idx}, event)">Save Lot</button>
              <button class="btn-lot-cancel" onclick="closeAddLot(${idx}, event)">Cancel</button>
            </div>
          </div>
          <div class="holdings-table-wrap" id="htw-${idx}">
            ${buildHoldingsTable(r, idx)}
          </div>
        </div>
      </div>
    </div>`;
  }).join('');
}

// ═══════════════════════════════════
//  HOLDINGS TABLE BUILDER
// ═══════════════════════════════════
function buildHoldingsTable(r, idx) {
  const lots = r.lots || [];
  if (lots.length === 0) {
    return `<p style="font-size:0.8rem;color:var(--muted);font-style:italic;padding:8px 0;">No purchase lots recorded. Click "Add Purchase Lot" to begin tracking.</p>`;
  }

  const cp = r.currentPrice || null; // {price, currency, asOf}

  let totalCost = 0, totalValue = 0, hasPrice = !!cp;

  const rows = lots.map((lot, li) => {
    const buyPrice = parseFloat(lot.price) || 0;
    const units    = parseFloat(lot.units) || 0;
    const cost     = buyPrice * units;
    totalCost += cost;

    let valueCell = '—', pnlCell = '—', pctCell = '—', pnlClass = 'pnl-neu';
    if (hasPrice && cp.currency === lot.currency) {
      const value = cp.price * units;
      totalValue += value;
      const pnl   = value - cost;
      const pct   = cost > 0 ? ((pnl / cost) * 100) : 0;
      pnlClass  = pnl >= 0 ? 'pnl-pos' : 'pnl-neg';
      valueCell = `${symFor(lot.currency)}${fmt(value)}`;
      pnlCell   = `${pnl >= 0 ? '+' : ''}${symFor(lot.currency)}${fmt(Math.abs(pnl))}`;
      pctCell   = `${pct >= 0 ? '+' : ''}${pct.toFixed(2)}%`;
    } else if (hasPrice && cp.currency !== lot.currency) {
      valueCell = pnlCell = pctCell = '≠ CCY';
    }

    return `<tr>
      <td>${lot.date || '—'}</td>
      <td>${esc(lot.currency)}</td>
      <td>${symFor(lot.currency)}${fmt(buyPrice)}</td>
      <td>${fmt(units)}</td>
      <td>${symFor(lot.currency)}${fmt(cost)}</td>
      <td>${hasPrice && cp.currency===lot.currency ? symFor(cp.currency)+fmt(cp.price) : '—'}</td>
      <td>${valueCell}</td>
      <td class="${pnlClass}">${pnlCell}</td>
      <td class="${pnlClass}">${pctCell}</td>
      <td><button class="btn-del-lot" onclick="deleteLot(${idx},${li},event)" title="Remove lot">✕</button></td>
    </tr>`;
  });

  // Total row
  let totalRow = '';
  if (lots.length > 1) {
    let totalPnl = '—', totalPct = '—', totalPnlClass = 'pnl-neu', totalValCell = '—';
    if (hasPrice && totalValue > 0) {
      const pnl = totalValue - totalCost;
      const pct = totalCost > 0 ? ((pnl / totalCost) * 100) : 0;
      totalPnlClass = pnl >= 0 ? 'pnl-pos' : 'pnl-neg';
      totalValCell = `${symFor(cp.currency)}${fmt(totalValue)}`;
      totalPnl = `${pnl>=0?'+':''}${symFor(cp.currency)}${fmt(Math.abs(pnl))}`;
      totalPct = `${pct>=0?'+':''}${pct.toFixed(2)}%`;
    }
    totalRow = `<tr class="holdings-total">
      <td colspan="4">Total</td>
      <td>${symFor(lots[0].currency)}${fmt(totalCost)}</td>
      <td></td>
      <td>${totalValCell}</td>
      <td class="${totalPnlClass}">${totalPnl}</td>
      <td class="${totalPnlClass}">${totalPct}</td>
      <td></td>
    </tr>`;
  }

  return `<table class="holdings-table">
    <thead><tr>
      <th style="text-align:left;">Purchase Date</th>
      <th>CCY</th>
      <th>Buy Price</th>
      <th>Units</th>
      <th>Cost Basis</th>
      <th>Current Price</th>
      <th>Holding Value</th>
      <th>P&amp;L</th>
      <th>% Change</th>
      <th></th>
    </tr></thead>
    <tbody>${rows.join('')}${totalRow}</tbody>
  </table>`;
}

function symFor(ccy) {
  const m = {USD:'$',GBP:'£',EUR:'€',JPY:'¥',CAD:'C$',AUD:'A$',CHF:'Fr',HKD:'HK$',INR:'₹',SEK:'kr',NOK:'kr',DKK:'kr'};
  return m[ccy] || (ccy ? ccy+' ' : '');
}
function fmt(n) {
  if (n === null || n === undefined || isNaN(n)) return '—';
  return parseFloat(n).toLocaleString('en-GB', {minimumFractionDigits:2, maximumFractionDigits:2});
}

// ═══════════════════════════════════
//  HOLDINGS INTERACTIONS
// ═══════════════════════════════════
function toggleHoldings(idx, e) {
  e.stopPropagation();
  const body = document.getElementById('hb-' + idx);
  const btn  = document.getElementById('ht-btn-' + idx);
  const open = body.classList.toggle('open');
  btn.classList.toggle('open', open);
}

function openAddLot(idx, e) {
  e.stopPropagation();
  document.getElementById('alf-' + idx).classList.add('open');
  // Default date to today
  const di = document.getElementById('lf-date-' + idx);
  if (di && !di.value) di.value = new Date().toISOString().slice(0,10);
}
function closeAddLot(idx, e) {
  if (e) e.stopPropagation();
  document.getElementById('alf-' + idx).classList.remove('open');
}

async function saveLot(idx, e) {
  e.stopPropagation();
  const r     = _portfolioCache[idx];
  if (!r) return;

  const date  = document.getElementById('lf-date-' + idx).value;
  const cur   = document.getElementById('lf-cur-' + idx).value;
  const price = parseFloat(document.getElementById('lf-price-' + idx).value);
  const units = parseFloat(document.getElementById('lf-units-' + idx).value);

  if (!date || isNaN(price) || isNaN(units) || price <= 0 || units <= 0) {
    alert('Please fill in all fields with valid values.');
    return;
  }

  if (!r.lots) r.lots = [];
  r.lots.push({ date, currency: cur, price, units });
  await saveAnalysis(r.email || _currentEmail, r);
  _portfolioCache[idx] = r;

  // Reset & close form
  document.getElementById('lf-date-' + idx).value  = '';
  document.getElementById('lf-price-' + idx).value = '';
  document.getElementById('lf-units-' + idx).value = '';
  closeAddLot(idx, e);

  document.getElementById('htw-' + idx).innerHTML = buildHoldingsTable(r, idx);
  updateHoldingsSummary(idx, r);
}

async function deleteLot(idx, li, e) {
  e.stopPropagation();
  const r = _portfolioCache[idx];
  if (!r || !r.lots) return;
  if (!confirm('Remove this lot?')) return;
  r.lots.splice(li, 1);
  await saveAnalysis(r.email || _currentEmail, r);
  _portfolioCache[idx] = r;
  document.getElementById('htw-' + idx).innerHTML = buildHoldingsTable(r, idx);
  updateHoldingsSummary(idx, r);
}

function updateHoldingsSummary(idx, r) {
  const lots = r.lots || [];
  const el   = document.getElementById('ht-summary-' + idx);
  if (!el) return;
  el.textContent = lots.length > 0
    ? `${lots.length} lot${lots.length>1?'s':''} · ${lots.reduce((s,l)=>s+(parseFloat(l.units)||0),0)} units`
    : 'No lots — click to add';
}

// ═══════════════════════════════════
//  PRICE REFRESH (via Claude API)
// ═══════════════════════════════════
async function refreshOneLot(idx, e) {
  e.stopPropagation();
  const r = _portfolioCache[idx];
  if (!r) return;
  const btn = document.getElementById('rfbtn-' + idx);
  btn.classList.add('loading');
  btn.textContent = '⟳ Fetching…';
  try {
    const priceData = await fetchCurrentPrice(r.ticker, r.company);
    r.currentPrice  = priceData;
    r.pricesAsOf    = priceData.asOf;
    await saveAnalysis(r.email || _currentEmail, r);
    _portfolioCache[idx] = r;
    document.getElementById('htw-' + idx).innerHTML = buildHoldingsTable(r, idx);
    const asOfEl = document.getElementById('price-as-of-' + idx);
    if (asOfEl) asOfEl.textContent = 'Prices as of ' + priceData.asOf;
  } catch(err) {
    alert('Could not fetch price: ' + err.message);
  } finally {
    btn.classList.remove('loading');
    btn.textContent = '⟳ Refresh Price';
  }
}

async function refreshAllPrices() {
  if (!_currentEmail) return;
  const btn    = document.getElementById('btn-refresh-all');
  const status = document.getElementById('refresh-all-status');
  btn.style.opacity = '0.5';
  btn.style.pointerEvents = 'none';

  const tickers = _portfolioCache
    .map((r,i) => ({ticker: r.ticker, company: r.company, idx: i}))
    .filter(x => x.ticker);

  let done = 0;
  for (const t of tickers) {
    status.textContent = `Refreshing ${t.ticker}… (${done+1}/${tickers.length})`;
    try {
      const priceData = await fetchCurrentPrice(t.ticker, t.company);
      const r = _portfolioCache[t.idx];
      r.currentPrice = priceData;
      r.pricesAsOf   = priceData.asOf;
      await saveAnalysis(r.email || _currentEmail, r);
      _portfolioCache[t.idx] = r;
      // Update table if rendered
      const htw = document.getElementById('htw-' + t.idx);
      if (htw) htw.innerHTML = buildHoldingsTable(r, t.idx);
      const asOfEl = document.getElementById('price-as-of-' + t.idx);
      if (asOfEl) asOfEl.textContent = 'Prices as of ' + priceData.asOf;
    } catch(err) {
      console.warn('Price fetch failed for', t.ticker, err.message);
    }
    done++;
  }

  btn.style.opacity = '';
  btn.style.pointerEvents = '';
  status.textContent = `Updated ${done} stock${done!==1?'s':''} · ${new Date().toLocaleTimeString('en-GB',{hour:'2-digit',minute:'2-digit'})}`;
}

async function fetchCurrentPrice(ticker, company) {
  const today = new Date().toLocaleDateString('en-GB', {day:'numeric', month:'long', year:'numeric'});
  const prompt = `You have access to current stock market data. Provide the current stock price for ${ticker}${company ? ' (' + company + ')' : ''} as of today (${today}).

Return ONLY a valid JSON object with no markdown or preamble:
{
  "ticker": "${ticker}",
  "price": <current price as a number, e.g. 142.56>,
  "currency": "<3-letter ISO currency code the stock trades in, e.g. USD, GBP, EUR>",
  "exchange": "<exchange name, e.g. NYSE, NASDAQ, LSE>",
  "asOf": "<date string, e.g. 21 March 2026>"
}

Use your most current knowledge. If you are uncertain, provide your best estimate and note it in the asOf field with "(est.)". Return ONLY the raw JSON.`;

  const resp = await fetch('https://api.anthropic.com/v1/messages', {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({
      model: 'claude-sonnet-4-20250514',
      max_tokens: 300,
      messages: [{ role: 'user', content: prompt }]
    })
  });

  if (!resp.ok) throw new Error('HTTP ' + resp.status);
  const data = await resp.json();
  const text = (data.content || []).map(b => b.text || '').join('');
  const start = text.indexOf('{');
  const end   = text.lastIndexOf('}');
  if (start === -1 || end === -1) throw new Error('No JSON in response');
  const parsed = JSON.parse(text.slice(start, end + 1));
  if (!parsed.price || isNaN(parsed.price)) throw new Error('Invalid price returned');
  return parsed;
}

// ═══════════════════════════════════
//  RESTORE & LOGOUT
// ═══════════════════════════════════
function restoreFromPortfolio(idx) {
  const r = _portfolioCache[idx];
  if (!r) return;
  _currentAnalysis = r;
  switchTab('analyse', document.getElementById('tab-btn-analyse'));
  renderResult(r);
}

function logoutPortfolio() {
  _currentEmail = null;
  _portfolioCache = [];
  document.getElementById('portfolio-login-section').style.display = 'block';
  document.getElementById('portfolio-view').style.display = 'none';
  document.getElementById('portfolio-email-inp').value = '';
}
</script>

<!-- ══════════════ ADMIN PAGE ══════════════ -->
<div id="page-admin">

  <nav class="admin-nav">
    <div class="admin-nav-brand">
      <div class="admin-nav-logo">Graham <em>Signal</em></div>
      <div class="admin-badge">ADMIN CONSOLE</div>
    </div>
    <div class="admin-nav-right">
      <span class="admin-nav-user" id="admin-nav-user"></span>
      <button class="btn-back-site" onclick="exitAdmin()">← Back to Site</button>
      <button class="btn-admin-logout" id="admin-logout-btn" onclick="adminLogout()" style="display:none;">Sign Out</button>
    </div>
  </nav>

  <!-- LOGIN -->
  <div id="admin-login">
    <div class="admin-login-card">
      <div class="admin-login-icon">🔐</div>
      <h2>Admin Access</h2>
      <p>This area is restricted to authorised administrators of Graham Signal.</p>

      <div id="admin-login-form">
        <div class="admin-field">
          <label>Username</label>
          <input type="text" id="admin-username" placeholder="Enter username" autocomplete="off">
        </div>
        <div class="admin-field">
          <label>Password</label>
          <input type="password" id="admin-password" placeholder="Enter password" onkeydown="if(event.key==='Enter')attemptAdminLogin()">
        </div>
        <button class="btn-admin-login" onclick="attemptAdminLogin()">Access Console →</button>
        <div class="admin-login-err" id="admin-login-err"></div>
        <div class="admin-forgot">
          <a onclick="showResetPanel()">Forgot password? Send reset code</a>
        </div>
      </div>

      <!-- PASSWORD RESET -->
      <div id="admin-reset-panel">
        <div class="admin-reset-info">
          A 6-digit reset code will be sent to <strong><a href="/cdn-cgi/l/email-protection" class="__cf_email__" data-cfemail="a9c2c8dbdcc4dac8c7cdc1dce9c1c6ddc4c8c0c587cac6c4">[email&#160;protected]</a></strong>. Enter it below to set a new password.
        </div>
        <div id="admin-reset-step1">
          <button class="btn-admin-login" id="btn-send-reset" onclick="sendResetCode()" style="margin-bottom:12px;">Send Reset Code →</button>
        </div>
        <div id="admin-reset-step2" style="display:none;">
          <div class="admin-field">
            <label>Reset Code</label>
            <div class="admin-field-row">
              <input type="text" id="admin-reset-code-inp" placeholder="6-digit code" maxlength="6">
              <button class="btn-verify-code" onclick="verifyResetCode()">Verify</button>
            </div>
          </div>
        </div>
        <div id="admin-reset-step3" style="display:none;">
          <div class="admin-field">
            <label>New Password</label>
            <input type="password" id="admin-new-pw" placeholder="Enter new password">
          </div>
          <div class="admin-field">
            <label>Confirm Password</label>
            <input type="password" id="admin-confirm-pw" placeholder="Confirm new password">
          </div>
          <button class="btn-admin-login" onclick="saveNewPassword()">Save New Password →</button>
        </div>
        <div class="admin-login-err" id="admin-reset-err"></div>
        <div style="text-align:center;margin-top:16px;">
          <a style="font-size:0.8rem;color:rgba(232,228,220,0.35);cursor:pointer;" onclick="cancelReset()">← Back to login</a>
        </div>
      </div>

    </div>
  </div>

  <!-- CONSOLE -->
  <div id="admin-console">
    <div class="admin-container">

      <!-- EmailJS config -->
      <div class="admin-ejs-panel" id="admin-ejs-panel">
        <div class="admin-ejs-title">📧 EmailJS Configuration — Password Reset</div>
        <div class="admin-ejs-row">
          <div class="admin-ejs-group">
            <label>Public Key</label>
            <input type="text" id="aejs-pubkey" placeholder="e.g. user_xxxxxxxxxx">
          </div>
          <div class="admin-ejs-group">
            <label>Service ID</label>
            <input type="text" id="aejs-service" placeholder="e.g. service_xxxxxxx">
          </div>
          <div class="admin-ejs-group">
            <label>Template ID</label>
            <input type="text" id="aejs-template" placeholder="e.g. template_xxxxxxx">
          </div>
        </div>
        <button class="btn-save-ejs" onclick="saveAdminEJS()">Save Configuration</button>
        <div class="ejs-status" id="aejs-status">✓ Configuration saved</div>
      </div>

      <!-- Stats -->
      <div class="admin-stats-bar" id="admin-stats-bar"></div>

      <!-- Accounts -->
      <div class="admin-section-title">Registered Accounts</div>
      <div class="admin-search-row">
        <input type="text" class="admin-search" id="admin-search" placeholder="Search by email…" oninput="adminSearch(this.value)">
      </div>
      <div class="admin-accounts-list" id="admin-accounts-list"></div>

    </div>
  </div>

</div><!-- /#page-admin -->

<script data-cfasync="false" src="/cdn-cgi/scripts/5c5dd728/cloudflare-static/email-decode.min.js"></script><script>
// ═══════════════════════════════════════════
//  ADMIN — CONSTANTS & STATE
// ═══════════════════════════════════════════

// Credentials stored obfuscated. Note: client-side only — not cryptographically secure.
const _A = { u: atob('a2R3YWdnODU='), p: atob('TDMzZDVMMzNkNUwzM2Q1') };
const ADMIN_EJS_KEY  = 'gs_admin_ejs';
const ADMIN_PW_KEY   = 'gs_admin_pw_override';
let   _adminCode     = null;
let   _adminAuthed   = false;
let   _adminAllAccounts = [];

// ═══════════════════════════════════════════
//  HASH ROUTING
// ═══════════════════════════════════════════
function checkAdminRoute() {
  if (window.location.hash === '#admin') showAdminPage();
}
window.addEventListener('hashchange', checkAdminRoute);
window.addEventListener('DOMContentLoaded', checkAdminRoute);

function showAdminPage() {
  document.getElementById('page-home').style.display  = 'none';
  document.getElementById('page-app').style.display   = 'none';
  document.getElementById('page-admin').style.display = 'block';
  window.scrollTo(0,0);
  if (_adminAuthed) showAdminConsole();
}

function exitAdmin() {
  window.location.hash = '';
  document.getElementById('page-admin').style.display = 'none';
  document.getElementById('page-home').style.display  = 'block';
  window.scrollTo(0,0);
}

// ═══════════════════════════════════════════
//  ADMIN LOGIN
// ═══════════════════════════════════════════
let _loginAttempts = 0;
let _lockUntil     = 0;

function attemptAdminLogin() {
  const errEl = document.getElementById('admin-login-err');
  errEl.style.display = 'none';

  if (Date.now() < _lockUntil) {
    const secs = Math.ceil((_lockUntil - Date.now()) / 1000);
    errEl.textContent = `Too many attempts. Try again in ${secs}s.`;
    errEl.style.display = 'block'; return;
  }

  const u = (document.getElementById('admin-username').value || '').trim();
  const p = (document.getElementById('admin-password').value || '').trim();

  // Check override password first (post-reset)
  const pwOverride = localStorage.getItem(ADMIN_PW_KEY);
  const correctPw  = pwOverride || _A.p;

  if (u === _A.u && p === correctPw) {
    _loginAttempts = 0;
    _adminAuthed   = true;
    showAdminConsole();
  } else {
    _loginAttempts++;
    if (_loginAttempts >= 5) {
      _lockUntil = Date.now() + 60000;
      errEl.textContent = 'Too many failed attempts. Locked for 60 seconds.';
    } else {
      errEl.textContent = `Invalid credentials. ${5 - _loginAttempts} attempt${5-_loginAttempts!==1?'s':''} remaining.`;
    }
    errEl.style.display = 'block';
    document.getElementById('admin-password').value = '';
  }
}

function adminLogout() {
  _adminAuthed = false;
  document.getElementById('admin-console').style.display = 'none';
  document.getElementById('admin-login').style.display   = 'flex';
  document.getElementById('admin-logout-btn').style.display = 'none';
  document.getElementById('admin-nav-user').textContent  = '';
  document.getElementById('admin-username').value = '';
  document.getElementById('admin-password').value = '';
}

// ═══════════════════════════════════════════
//  PASSWORD RESET
// ═══════════════════════════════════════════
function showResetPanel() {
  document.getElementById('admin-login-form').style.display = 'none';
  document.getElementById('admin-reset-panel').style.display = 'block';
}
function cancelReset() {
  document.getElementById('admin-reset-panel').style.display = 'none';
  document.getElementById('admin-login-form').style.display  = 'block';
  document.getElementById('admin-reset-err').style.display   = 'none';
  document.getElementById('admin-reset-step2').style.display = 'none';
  document.getElementById('admin-reset-step3').style.display = 'none';
  document.getElementById('admin-reset-step1').style.display = 'block';
}

async function sendResetCode() {
  const errEl = document.getElementById('admin-reset-err');
  const btn   = document.getElementById('btn-send-reset');
  errEl.style.display = 'none';
  btn.textContent = 'Sending…'; btn.disabled = true;

  _adminCode = String(Math.floor(100000 + Math.random() * 900000));

  const cfg = getAdminEJS();
  if (!cfg.publicKey || !cfg.serviceId || !cfg.templateId) {
    errEl.textContent = 'EmailJS not configured. Set it up in the Admin Console first, or contact your developer.';
    errEl.style.display = 'block';
    btn.textContent = 'Send Reset Code →'; btn.disabled = false;
    return;
  }

  try {
    const resp = await fetch('https://api.emailjs.com/api/v1.0/email/send', {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({
        service_id:  cfg.serviceId,
        template_id: cfg.templateId,
        user_id:     cfg.publicKey,
        template_params: {
          to_email: 'karumsandhu@hotmail.com',
          subject:  'Graham Signal — Admin Password Reset Code',
          message:  `Your Graham Signal admin password reset code is: ${_adminCode}\n\nThis code is valid for 10 minutes. If you did not request this, ignore this email.`
        }
      })
    });
    if (resp.ok || resp.status === 200) {
      document.getElementById('admin-reset-step1').style.display = 'none';
      document.getElementById('admin-reset-step2').style.display = 'block';
    } else {
      throw new Error('EmailJS returned status ' + resp.status);
    }
  } catch(e) {
    errEl.textContent = 'Failed to send email: ' + e.message;
    errEl.style.display = 'block';
  } finally {
    btn.textContent = 'Send Reset Code →'; btn.disabled = false;
  }

  // Auto-expire code after 10 minutes
  setTimeout(() => { _adminCode = null; }, 600000);
}

function verifyResetCode() {
  const input = (document.getElementById('admin-reset-code-inp').value || '').trim();
  const errEl = document.getElementById('admin-reset-err');
  if (!_adminCode) {
    errEl.textContent = 'Reset code has expired. Please request a new one.';
    errEl.style.display = 'block'; return;
  }
  if (input !== _adminCode) {
    errEl.textContent = 'Incorrect code. Please check and try again.';
    errEl.style.display = 'block'; return;
  }
  errEl.style.display = 'none';
  document.getElementById('admin-reset-step2').style.display = 'none';
  document.getElementById('admin-reset-step3').style.display = 'block';
}

function saveNewPassword() {
  const pw1   = document.getElementById('admin-new-pw').value;
  const pw2   = document.getElementById('admin-confirm-pw').value;
  const errEl = document.getElementById('admin-reset-err');
  if (pw1.length < 8) {
    errEl.textContent = 'Password must be at least 8 characters.';
    errEl.style.display = 'block'; return;
  }
  if (pw1 !== pw2) {
    errEl.textContent = 'Passwords do not match.';
    errEl.style.display = 'block'; return;
  }
  localStorage.setItem(ADMIN_PW_KEY, pw1);
  _adminCode = null;
  alert('Password updated successfully. Please log in with your new password.');
  cancelReset();
}

// ═══════════════════════════════════════════
//  EMAILJS CONFIG (admin)
// ═══════════════════════════════════════════
function getAdminEJS() {
  try { return JSON.parse(localStorage.getItem(ADMIN_EJS_KEY) || '{}'); } catch { return {}; }
}
function saveAdminEJS() {
  const cfg = {
    publicKey:  document.getElementById('aejs-pubkey').value.trim(),
    serviceId:  document.getElementById('aejs-service').value.trim(),
    templateId: document.getElementById('aejs-template').value.trim()
  };
  localStorage.setItem(ADMIN_EJS_KEY, JSON.stringify(cfg));
  const s = document.getElementById('aejs-status');
  s.style.display = 'block';
  setTimeout(() => s.style.display = 'none', 2500);
}
function loadAdminEJSFields() {
  const cfg = getAdminEJS();
  if (cfg.publicKey)  document.getElementById('aejs-pubkey').value  = cfg.publicKey;
  if (cfg.serviceId)  document.getElementById('aejs-service').value = cfg.serviceId;
  if (cfg.templateId) document.getElementById('aejs-template').value = cfg.templateId;
}

// ═══════════════════════════════════════════
//  SHOW CONSOLE
// ═══════════════════════════════════════════
function showAdminConsole() {
  document.getElementById('admin-login').style.display   = 'none';
  document.getElementById('admin-console').style.display = 'block';
  document.getElementById('admin-logout-btn').style.display = 'block';
  document.getElementById('admin-nav-user').textContent  = _A.u;
  loadAdminEJSFields();
  loadAllAccounts();
}

// ═══════════════════════════════════════════
//  LOAD ALL ACCOUNTS FROM LOCALSTORAGE
// ═══════════════════════════════════════════
async function loadAllAccounts() {
  const list = document.getElementById('admin-accounts-list');
  list.innerHTML = '<div class="admin-empty" style="padding:32px;text-align:center;">Loading accounts from Firebase…</div>';

  const fb = window._fb;
  let accounts = [];

  if (fb) {
    const fbAccounts = await fb.loadAll();
    if (fbAccounts !== null) {
      accounts = fbAccounts.sort((a,b) => (b.records||[]).length - (a.records||[]).length);
    } else {
      // Firebase offline — fall back to localStorage
      try {
        const raw = localStorage.getItem(STORAGE_KEY);
        const all = raw ? JSON.parse(raw) : {};
        accounts = Object.entries(all).map(([email, records]) => ({ email, records: records||[] }))
                         .sort((a,b) => b.records.length - a.records.length);
        list.innerHTML += '<div style="font-size:0.75rem;color:rgba(232,228,220,0.35);padding:0 24px 16px;">⚠ Firebase offline — showing local cache only</div>';
      } catch(e) { accounts = []; }
    }
  } else {
    // Firebase SDK not yet ready — wait briefly then try again
    await new Promise(r => setTimeout(r, 1500));
    return loadAllAccounts();
  }

  _adminAllAccounts = accounts;
  renderAdminView(accounts);
}

function renderAdminView(accounts) {
  // Stats
  const totalAccounts = accounts.length;
  const totalAnalyses = accounts.reduce((s,a) => s + a.records.length, 0);
  const totalLots     = accounts.reduce((s,a) => s + a.records.reduce((r,rec) => r + (rec.lots||[]).length, 0), 0);
  const avgScore      = totalAnalyses > 0
    ? Math.round(accounts.reduce((s,a) => s + a.records.reduce((r,rec) => r + (rec.overall_score||0), 0), 0) / totalAnalyses)
    : '—';

  document.getElementById('admin-stats-bar').innerHTML = `
    <div class="admin-stat"><div class="admin-stat-val">${totalAccounts}</div><div class="admin-stat-lbl">Accounts</div></div>
    <div class="admin-stat"><div class="admin-stat-val">${totalAnalyses}</div><div class="admin-stat-lbl">Total Analyses</div></div>
    <div class="admin-stat"><div class="admin-stat-val">${totalLots}</div><div class="admin-stat-lbl">Holdings Lots</div></div>
    <div class="admin-stat"><div class="admin-stat-val">${avgScore}</div><div class="admin-stat-lbl">Avg Graham Score</div></div>
  `;

  const list = document.getElementById('admin-accounts-list');
  if (accounts.length === 0) {
    list.innerHTML = '<div class="admin-empty">No registered accounts found in local storage.</div>';
    return;
  }
  list.innerHTML = accounts.map((acct, ai) => renderAdminAccountCard(acct, ai)).join('');
}

function renderAdminAccountCard(acct, ai) {
  const { email, records } = acct;
  const totalLots = records.reduce((s,r) => s + (r.lots||[]).length, 0);
  const avgScore  = records.length > 0
    ? Math.round(records.reduce((s,r) => s + (r.overall_score||0),0) / records.length)
    : '—';
  const lastDate  = records.length > 0 ? records[0].date : '—';

  const stockRows = records.map((rec, ri) => {
    const lots     = rec.lots || [];
    const scoreClass = (rec.overall_score||0) >= 65 ? 'admin-score-high'
                     : (rec.overall_score||0) >= 45 ? 'admin-score-mid' : 'admin-score-low';
    const tagHtml  = rec.tag
      ? `<span class="admin-tag-pill admin-tag-${adminEsc(rec.tag)}">${rec.tag==='watch'?'👁 Watch':rec.tag==='bought'?'✅ Bought':'📤 Sold'}</span>`
      : '<span style="color:rgba(232,228,220,0.2);font-size:0.72rem;">—</span>';

    const holdingsMini = lots.length > 0 ? `
      <div class="admin-holdings-mini">
        <table>
          <thead><tr>
            <th style="text-align:left;">Date</th>
            <th>CCY</th><th>Buy Price</th><th>Units</th><th>Cost</th>
            <th>Curr. Price</th><th>Value</th><th>P&L</th><th>%</th>
          </tr></thead>
          <tbody>
            ${lots.map(lot => {
              const bp  = parseFloat(lot.price)||0;
              const un  = parseFloat(lot.units)||0;
              const cp  = rec.currentPrice;
              const sym = adminSymFor(lot.currency);
              let val='—', pnl='—', pct='—', cls='';
              if (cp && cp.currency === lot.currency) {
                const v = cp.price * un;
                const g = v - bp*un;
                const p = bp*un > 0 ? (g/(bp*un)*100) : 0;
                cls  = g >= 0 ? 'color:#7ec8c8' : 'color:#e07070';
                val  = sym + adminFmt(v);
                pnl  = (g>=0?'+':'') + sym + adminFmt(Math.abs(g));
                pct  = (p>=0?'+':'') + p.toFixed(2) + '%';
              }
              return `<tr>
                <td>${adminEsc(lot.date||'—')}</td>
                <td>${adminEsc(lot.currency)}</td>
                <td>${sym}${adminFmt(bp)}</td>
                <td>${adminFmt(un)}</td>
                <td>${sym}${adminFmt(bp*un)}</td>
                <td>${cp && cp.currency===lot.currency ? adminSymFor(cp.currency)+adminFmt(cp.price) : '—'}</td>
                <td>${val}</td>
                <td style="${cls}">${pnl}</td>
                <td style="${cls}">${pct}</td>
              </tr>`;
            }).join('')}
          </tbody>
        </table>
      </div>` : '<div class="admin-no-holdings">No holdings recorded</div>';

    return `<div class="admin-stock-row" style="display:block;padding:14px 18px;">
      <div style="display:grid;grid-template-columns:80px 1fr auto auto auto;gap:12px;align-items:center;margin-bottom:${lots.length>0?'10px':'0'};">
        <div class="admin-stock-ticker">${adminEsc(rec.ticker)}</div>
        <div>
          <div class="admin-stock-company">${adminEsc(rec.company)}</div>
          <div class="admin-stock-date">${adminEsc(rec.date)}${rec.notes ? ' · 📝' : ''}</div>
        </div>
        <span class="admin-score-pill ${scoreClass}">${rec.overall_score}</span>
        <span style="font-size:0.75rem;color:rgba(232,228,220,0.4);">${adminEsc(rec.verdict_level||'')}</span>
        ${tagHtml}
      </div>
      ${holdingsMini}
    </div>`;
  }).join('');

  return `<div class="admin-account-card" id="aac-${ai}">
    <div class="admin-account-header" onclick="toggleAdminAccount(${ai})">
      <div>
        <div class="admin-account-email">${adminEsc(email)}</div>
        <div class="admin-account-meta">${records.length} stock${records.length!==1?'s':''} · Avg score ${avgScore} · ${totalLots} lot${totalLots!==1?'s':''} · Last active ${adminEsc(lastDate)}</div>
      </div>
      <div class="admin-account-actions" onclick="event.stopPropagation()">
        <button class="admin-expand-btn" onclick="toggleAdminAccount(${ai})">▼ View Portfolio</button>
        <button class="btn-admin-export" onclick="exportAccount('${adminEsc(email)}')">⬇ Export JSON</button>
        <button class="btn-admin-delete" onclick="deleteAccount('${adminEsc(email)}', ${ai})">🗑 Delete</button>
      </div>
    </div>
    <div class="admin-account-detail" id="aad-${ai}">
      <div class="admin-portfolio-inner">
        ${records.length === 0
          ? '<div class="admin-empty">No analyses in this portfolio.</div>'
          : `<div class="admin-stock-list">${stockRows}</div>`}
      </div>
    </div>
  </div>`;
}

function toggleAdminAccount(ai) {
  const detail = document.getElementById('aad-' + ai);
  const btn    = document.querySelector(`#aac-${ai} .admin-expand-btn`);
  const open   = detail.classList.toggle('open');
  if (btn) btn.textContent = open ? '▲ Hide Portfolio' : '▼ View Portfolio';
}

// ═══════════════════════════════════════════
//  ADMIN SEARCH
// ═══════════════════════════════════════════
function adminSearch(query) {
  const q = query.toLowerCase().trim();
  const filtered = q
    ? _adminAllAccounts.filter(a => a.email.toLowerCase().includes(q))
    : _adminAllAccounts;
  renderAdminView(filtered);
}

// ═══════════════════════════════════════════
//  ADMIN ACTIONS
// ═══════════════════════════════════════════
async function exportAccount(email) {
  try {
    const records = await getStorageRecords(email);
    const data = { email, exportedAt: new Date().toISOString(), records };
    const blob = new Blob([JSON.stringify(data, null, 2)], { type: 'application/json' });
    const url  = URL.createObjectURL(blob);
    const a    = document.createElement('a');
    a.href     = url;
    a.download = `graham-signal-${email.replace(/[@.]/g,'-')}.json`;
    a.click();
    URL.revokeObjectURL(url);
  } catch(e) { alert('Export failed: ' + e.message); }
}

async function deleteAccount(email, ai) {
  if (!confirm(`Permanently delete ALL data for ${email}?\n\nThis removes data from Firebase and cannot be undone.`)) return;
  try {
    await deleteAccountData(email);
    _adminAllAccounts = _adminAllAccounts.filter((_, i) => i !== ai);
    renderAdminView(_adminAllAccounts);
  } catch(e) { alert('Delete failed: ' + e.message); }
}

// ═══════════════════════════════════════════
//  ADMIN UTILS
// ═══════════════════════════════════════════
function adminEsc(s) {
  if (!s) return '';
  return String(s).replace(/&/g,'&amp;').replace(/</g,'&lt;').replace(/>/g,'&gt;').replace(/"/g,'&quot;');
}
function adminSymFor(ccy) {
  const m={USD:'$',GBP:'£',EUR:'€',JPY:'¥',CAD:'C$',AUD:'A$',CHF:'Fr',HKD:'HK$',INR:'₹',SEK:'kr',NOK:'kr',DKK:'kr'};
  retur
