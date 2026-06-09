# Clinical ML Portfolio

Una raccolta di mini-progetti di **machine learning per imaging biomedico e clinico**.
Ogni progetto è un repository indipendente, autocontenuto e riproducibile: una tecnica,
un caso d'uso, metriche reali con un baseline di confronto.

Principi comuni a tutti i progetti:

- **Riproducibilità**: ambiente gestito con [`uv`](https://docs.astral.sh/uv/) e `uv.lock`
  versionato, seed fissi.
- **Dati tracciabili**: dataset pubblici con licenza citata oppure dati sintetici con
  ground truth generata dal codice. I dataset pesanti non sono committati: si rigenerano o
  scaricano con `scripts/download_data.py`.
- **Valutazione onesta**: ogni progetto riporta metriche quantitative reali
  (`results/metrics.json`) confrontate con un baseline, e dichiara apertamente i limiti.
- **Demo interattiva**: ogni progetto include un'app [Streamlit](https://streamlit.io/)
  (`app/main.py`).
- **Qualità**: lint con `ruff`, test con `pytest`, CI GitHub Actions.

## Progetti

| Progetto | Tema | Dataset | Risultato chiave (vs baseline) |
|---|---|---|---|
| [cxr-xai-uncertainty](https://github.com/mdegiulio97/cxr-xai-uncertainty) | Classificazione + XAI + incertezza | PneumoniaMNIST (CC BY 4.0) | AUROC **0.963** vs 0.927 (logistica); incertezza MC-Dropout 3× più alta sugli errori; ECE 0.172 → 0.146 con temperature scaling |
| [genai-mri-superres](https://github.com/mdegiulio97/genai-mri-superres) | GenAI / super-risoluzione image-to-image | BloodMNIST 64² (CC BY 4.0) | PSNR **36.65 dB** vs 34.47 dB (bicubico), +2.17 dB; SSIM 0.962 vs 0.951 |
| [phasor-flim-explorer](https://github.com/mdegiulio97/phasor-flim-explorer) | Phasor analysis fit-free (FLIM) | Sintetico (ground truth) | MAE unmixing migliore del fitting NNLS a ogni livello di fotoni (es. 0.100 vs 0.136 @ 300 ph), ~**32×** più veloce |
| [pinn-qmri](https://github.com/mdegiulio97/pinn-qmri) | Physics-Informed NN / relaxometria T2 | Sintetico (ground truth) | Stima non supervisionata via physics loss; competitiva col least-squares ad alto rumore (RMSE 10.84 vs 12.46 @ σ=6) |
| [rag-radiology-reports](https://github.com/mdegiulio97/rag-radiology-reports) | RAG locale con citazioni | Referti sintetici | Hit-rate@1 **1.00** (12 query); anti-allucinazione 5/5 astensioni corrette su query fuori corpus |

Template riusabile da cui derivano i progetti:
[**ml-clinical-template**](https://github.com/mdegiulio97/ml-clinical-template) — struttura,
utility condivise (seeding) e CI.

## Come usare un progetto

```bash
git clone https://github.com/mdegiulio97/<progetto>.git
cd <progetto>
uv sync --extra dev
uv run pytest -q                       # test
uv run python scripts/download_data.py # dati (dove necessario)
uv run python scripts/evaluate.py      # metriche
uv run streamlit run app/main.py       # demo interattiva
```

## Nota

I progetti sono dimostrativi e didattici: i dati sono pubblici o sintetici e **non**
costituiscono strumenti clinici validati. L'uso di ciascun dataset è soggetto alla propria
licenza, indicata nel `LICENSE` e nel `README` del rispettivo progetto.

## Licenza

Codice rilasciato sotto licenza MIT (vedi il `LICENSE` di ciascun repository).
