# Come lanciare e valutare i progetti

Guida pratica per eseguire i 5 mini-progetti del portfolio in locale (Windows / PowerShell).
Prerequisito unico: **[uv](https://docs.astral.sh/uv/)** installato (gestisce Python 3.12 e
le dipendenze). Nessun altro setup.

> Verifica rapida che `uv` ci sia:
> ```powershell
> uv --version
> ```

---

## Flusso comune (vale per tutti i progetti)

Da PowerShell, spostati nella cartella del progetto e usa sempre `uv run` (così l'ambiente
è isolato e riproducibile):

```powershell
cd "C:\Users\Michele De Giulio\Desktop\clinical-ml-portfolio\<progetto>"

uv sync --extra dev                  # 1. crea l'ambiente con tutte le dipendenze
uv run pytest -q                     # 2. (opzionale) test: devono essere tutti verdi
uv run python scripts/evaluate.py    # 3. calcola le metriche -> results/metrics.json + figure
uv run streamlit run app/main.py     # 4. demo interattiva (si apre nel browser)
```

- I risultati numerici finiscono in `results/metrics.json`.
- Le figure (`.png`) finiscono in `results/` e sono le stesse mostrate nel README.
- L'app Streamlit si apre su http://localhost:8501 — chiudila con `Ctrl+C` nel terminale.

I dataset pesanti e i modelli **non** sono nel repo (sono rigenerabili/scaricabili): i passi
`download_data.py` / `train.py` qui sotto li ricreano quando servono.

---

## Istruzioni per progetto

### 1. cxr-xai-uncertainty — classificazione X-ray + spiegabilità + incertezza
Richiede download dati **e** training (il `model.pt` non è nel repo).
```powershell
cd "C:\Users\Michele De Giulio\Desktop\clinical-ml-portfolio\cxr-xai-uncertainty"
uv sync --extra dev
uv run python scripts/download_data.py    # scarica PneumoniaMNIST (CC BY 4.0)
uv run python scripts/train.py            # addestra ResNet18 -> results/model.pt
uv run python scripts/evaluate.py         # AUROC, ECE, incertezza, Grad-CAM
uv run streamlit run app/main.py
```
**Atteso:** AUROC ~0.963 vs baseline 0.927; ECE 0.172 -> 0.146; incertezza ~3x sugli errori.

### 2. genai-mri-superres — super-risoluzione immagini (ESPCN)
Richiede download dati **e** training.
```powershell
cd "C:\Users\Michele De Giulio\Desktop\clinical-ml-portfolio\genai-mri-superres"
uv sync --extra dev
uv run python scripts/download_data.py    # scarica BloodMNIST 64x64 (CC BY 4.0)
uv run python scripts/train.py --epochs 30
uv run python scripts/evaluate.py         # PSNR / SSIM vs bicubico
uv run streamlit run app/main.py
```
**Atteso:** PSNR ~36.65 dB vs 34.47 (bicubico), +2.17 dB; SSIM 0.962 vs 0.951.

### 3. phasor-flim-explorer — unmixing FLIM fit-free (il più veloce: nessun download/training)
```powershell
cd "C:\Users\Michele De Giulio\Desktop\clinical-ml-portfolio\phasor-flim-explorer"
uv sync --extra dev
uv run python scripts/evaluate.py         # dati sintetici generati al volo
uv run streamlit run app/main.py
```
**Atteso:** MAE del phasor migliore del fitting NNLS a ogni livello di fotoni, ~32x più veloce.

### 4. pinn-qmri — relaxometria T2 con physics loss (non supervisionata)
Il training è incluso nello script di valutazione (qualche minuto su CPU).
```powershell
cd "C:\Users\Michele De Giulio\Desktop\clinical-ml-portfolio\pinn-qmri"
uv sync --extra dev
uv run python scripts/download_data.py    # genera i dati sintetici
uv run python scripts/evaluate.py         # allena la PINN e confronta col least-squares
uv run streamlit run app/main.py
```
**Atteso:** a basso rumore il least-squares è migliore; a rumore alto (sigma=6) la PINN
vince in RMSE (10.84 vs 12.46).

### 5. rag-radiology-reports — RAG locale con citazioni (offline)
```powershell
cd "C:\Users\Michele De Giulio\Desktop\clinical-ml-portfolio\rag-radiology-reports"
uv sync --extra dev
uv run python scripts/download_data.py    # prepara il corpus sintetico
uv run python scripts/evaluate.py         # hit-rate@k + anti-allucinazione
uv run streamlit run app/main.py
```
**Atteso:** hit-rate@1 = 1.00 (12 query); 5/5 astensioni corrette su query fuori corpus.
Funziona 100% offline con TF-IDF; **opzionale** Ollama locale per risposte generative migliori
(altrimenti usa il fallback estrattivo automaticamente).

---

## Problemi comuni

- **`uv` non trovato** → installalo (vedi https://docs.astral.sh/uv/) e riapri il terminale.
- **Errore "Accesso negato" durante `uv sync`** (lock file Windows) → riprova il comando; se
  persiste chiudi eventuali app/Streamlit aperti che bloccano `.venv`, poi rilancia `uv sync`.
- **La porta 8501 è occupata** → `uv run streamlit run app/main.py --server.port 8502`.
- **`evaluate.py` di cxr/genai fallisce** → manca il modello: lancia prima `download_data.py`
  e `train.py`.

---

Repo online: https://github.com/mdegiulio97 (cerca i 5 progetti + `ml-clinical-template`).
Indice del portfolio: https://github.com/mdegiulio97/clinical-ml-portfolio
