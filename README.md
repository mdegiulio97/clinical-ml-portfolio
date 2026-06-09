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

## Anteprima

<table>
<tr>
<td width="50%"><a href="https://github.com/mdegiulio97/genai-mri-superres"><img src="https://raw.githubusercontent.com/mdegiulio97/genai-mri-superres/main/results/brain_examples.png" width="100%"/></a><br/><sub><b>genai-mri-superres</b> — super-risoluzione ×2 di MRI cerebrale T1w reale (bicubico / SR / HR)</sub></td>
<td width="50%"><a href="https://github.com/mdegiulio97/pinn-qmri"><img src="https://raw.githubusercontent.com/mdegiulio97/pinn-qmri/main/results/real_t2star_maps.png" width="100%"/></a><br/><sub><b>pinn-qmri</b> — mappe T2* su MRI cerebrale multi-echo reale (PINN vs least-squares)</sub></td>
</tr>
<tr>
<td width="50%"><a href="https://github.com/mdegiulio97/phasor-flim-explorer"><img src="https://raw.githubusercontent.com/mdegiulio97/phasor-flim-explorer/main/results/phasor_plot.png" width="100%"/></a><br/><sub><b>phasor-flim-explorer</b> — piano fasoriale e unmixing fit-free dei fluorofori</sub></td>
<td width="50%"><a href="https://github.com/mdegiulio97/cxr-xai-uncertainty"><img src="https://raw.githubusercontent.com/mdegiulio97/cxr-xai-uncertainty/main/results/tumor_gradcam.png" width="100%"/></a><br/><sub><b>cxr-xai-uncertainty</b> — Grad-CAM su MRI cerebrale (glioma vs non-glioma)</sub></td>
</tr>
</table>

## Progetti

| Progetto | Tema | Dataset | Risultato chiave (vs baseline) |
|---|---|---|---|
| [cxr-xai-uncertainty](https://github.com/mdegiulio97/cxr-xai-uncertainty) | Classificazione + XAI + incertezza | PneumoniaMNIST (CC BY 4.0) **+ MRI cerebrale Cheng** (glioma vs non-glioma, CC BY 4.0) | chest: AUROC 0.963; **brain: AUROC 0.980 vs 0.859, ECE 0.039→0.013, incertezza ~4.5× sugli errori** |
| [genai-mri-superres](https://github.com/mdegiulio97/genai-mri-superres) | GenAI / super-risoluzione image-to-image | BloodMNIST (CC BY 4.0) **+ MRI cerebrale T1w reale** (OpenNeuro ds007116, CC0) | benchmark: PSNR +2.17 dB vs bicubico; **brain reale (soggetto held-out): +0.52 dB, SSIM +0.007** |
| [phasor-flim-explorer](https://github.com/mdegiulio97/phasor-flim-explorer) | Phasor analysis fit-free (FLIM) | Sintetico (ground truth) | MAE unmixing migliore del fitting NNLS a ogni livello di fotoni (es. 0.100 vs 0.136 @ 300 ph), ~**32×** più veloce |
| [pinn-qmri](https://github.com/mdegiulio97/pinn-qmri) | Physics-Informed NN / relaxometria T2–T2* | Sintetico (ground truth) **+ MRI cerebrale multi-echo reale** (OpenNeuro ds007116, CC0) | sintetico: competitiva col LS ad alto rumore; **brain reale (validazione held-out echo): T2\* 50.4 ms, NRMSE 0.075 vs LS 0.100** |
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
