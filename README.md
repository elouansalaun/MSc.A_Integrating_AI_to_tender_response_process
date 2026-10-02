# Tender response process: Integration of artificial intelligence methods leveraging contractual and competitive data.

**Master of Applied Science (MSc.A), Industrial Engineering, Polytechnique Montréal, July 2026**<br>
Industry partner: a services company that wins most of its contracts through public tenders.

> **In one sentence:** I designed, built and evaluated an end-to-end, AI-based decision-support tool that helps bid managers find relevant public tenders, read 100–200-page contract PDFs automatically, find comparable past contracts and recommend a winning, profitable price.


*`Python` · `LLMs and RAG` · `NLP (BM25, sparse embeddings, spaCy)` · `Learning to Rank / XGBoost` · `Neural networks and learning from human feedback` · `Entity resolution` · `Web scraping (Selenium)` · `Streamlit` · `Docker` · `Azure (ADLS Gen2)` · `BPMN process modelling` · `Applied research with an industry partner`*


> The thesis was originally written in french, an automatic translation to english was made using AI tools `see /thesis`

## Abstract
<p align="justify">For many organizations, responding to public tenders is a central mechanism for winning contracts and a major determinant of their long-term viability. For our partner, the Commissionaires of Quebec (CCCQ), a leading player in the security and guarding services market with annual revenues of CAD 747 M and more than 22 million security hours worked in 2025, most of the contracts are awarded through public tenders. The quality of the response process, and more specifically, the relevance of the bid/no-bid decision and the proposed margin, directly conditions the organization's win rate and profitability.<p>

<p align="justify">Although this process generates enough crucial data, it has been weakly leveraged until now. Decisions relied mainly on analysts' experiences and perceptions. The literature review conducted in this thesis confirms that, while bid pricing modelling and bid/no-bid decision-making have been studied for several decades, the joint integration of automated textual information extraction,  contract  similarity  analysis,  and  bid  pricing  modelling  remains  poorly  documented  and  is particularly lacking in the security services sector.<p>

<p align="justify">In this context, our research aims to design a decision-support tool that leverages historical data to improve the tender response process. The work follows the Design Research Methodology (DRM) and is organized around five complementary modules developed in Python: (i) An anticipation and historical mining module based on SEAO web scraping and entity matching to anticipate tenders and retrieve similar past ones; (ii) a textual data valorization module relying on a Retrieval-Augmented Generation (RAG) architecture combining sparse embeddings, document hierarchy and  BM25;  (iii)  a  contract  similarity  module  trained  through  ranking-based  reinforcement (RankNet);  (iv)  a  Learning-to-Rank  pricing  model  that  jointly  accounts  for  the  technical characteristics of contracts and recent market dynamics; and (v) an interactive visualization tool ensuring the integration of all components into the business process.<p>

<p align="justify">The tool was evaluated on a representative sample of tenders published between 2023 and 2026. The  entity-matching  algorithm  achieves  a  92%  concordance  rate  when  retrieving  previous contracts. The pricing model, when restricted to economically viable margins, ranks in the top 3 bidders in 70% of cases and improves the CCCQ's ranking in 90% of the tenders compared to the internal reference. The comparison with historical decisions on 20 tenders highlights a substantial potential profit gain, provided that an expert review is conducted of recommendations that fall below the viability threshold of approximately 0.3. The proposed tool is not intended to replace human expertise, but rather to complement it by providing a quantitative, structured and systematic view of each tender's context. As such, it serves as a lever to optimize win rates and profitability while also facilitating prospecting and reading technical specifications.<p>


# Thesis summary: AI for the tender response process

> **In one sentence:** I designed, built and evaluated an end-to-end, AI-based decision-support tool that helps bid managers find relevant public tenders, read 100–200-page contract PDFs automatically, find comparable past contracts and recommend a winning, profitable price.

---

## The problem

- Bid/no-bid and pricing decisions relied mostly on **managers' intuition and experience**.
- Useful data existed but went **mostly unused** and **dispersed**: public tender results (SEAO, the Quebec government's tendering platform), internal history and long, unstructured PDF specifications.
- The research gap: the literature does not cover **text extraction, contract similarity and bid pricing combined in one tool**, and has nothing on the security services sector.

## What I built: a five-module tool in Python

| # | Module | What it does | Key technologies |
|---|---|---|---|
| 1 | **Prospecting and anticipation** | Daily scraping of open tenders; links each one to its past versions (renewals); predicts upcoming republications | Selenium web scraping, UNSPSC filtering, entity resolution (rules + Levenshtein + SequenceMatcher + Soundex), Internal docs (spreadsheets, notes) formatting|
| 2 | **Contract PDF extraction (RAG)** | Reads specifications and extracts the bidder's resource needs (uniforms, equipment, vehicles), then turns them into cost scores | **Hierarchical RAG**: PyMuPDF layout parsing, section-based chunking, sparse embeddings with domain vocabulary, **BM25 + hierarchy RAG**, **local LLM** (Ministral-3-14B-Instruct, quantized GGUF), spaCy cost matching |
| 3 | **Contract similarity** | Finds the past contracts most similar to the one under study | Distance per feature (custom matrices, Jaccard, time decay), small neural network **fine-tuned on ~300 expert rankings** (learning from human feedback, **RankNet** / Bradley-Terry loss) |
| 4 | **Pricing model** | Recommends the highest margin that still wins, based on similar contracts and recent market trends | **Learning to Rank with XGBoost (XGBRanker)** over a grid of candidate margins, market-adjusted features, chronological leave-one-out validation |
| 5 | **Decision dashboard** | Shows all indicators to managers: history, competitor profiles, margin trends and the recommendation | **Streamlit**, deployed with **Docker on Azure** (Azure Data Lake Storage Gen2, daily and weekly data refresh) |

**Design choices that mattered**
- **Tender type** Framing pricing as a *ranking* problem instead of a regression made better use of the dataset and captured the asymmetric goal: the highest bid that still wins.
- A **local, lightweight LLM** keeps confidential contract documents inside the company.
- **Sparse, domain-specific retrieval** beat standard dense embeddings on technical security vocabulary and reduced LLM hallucinations.
- The tool is **human-in-the-loop**: it supports managers' decisions and does not automate them.

## Process and methodology

- **Design Research Methodology (DRM)** in four phases: systematic literature review, then on-site analysis of the existing process, then iterative tool development, then evaluation on real historical cases.
- **Process modelling in BPMN 2.0**: I mapped the current (AS-IS) bid process with the partner's teams, found its weak points, and designed the new (TO-BE) process that includes the tool.
- **Realistic back-testing**: each test tender only uses data that would have been available at the time (two-month lag for results publication), so there is no data leakage.
- **Production rollout under way**: the prospecting module is being deployed in the partner's Azure environment.

## Results

| What was measured | Result |
|---|---|
| RAG extraction (50 contracts) | **98% recall, 87% precision** |
| Retrieval of past contracts (entity matching, 2025 tenders) | **92% match accuracy** |
| Pricing model win rate vs. the company's own decisions (20 test tenders) | **40% vs. 15%** |
| Average rank among bidders | **3.6 vs. 6.2** |
| Simulated gains on won contracts (same test set) | **CAD 3.5 M vs. 2.2 M** |
| 57 tenders, 2023–2024 (profitable margins only) | **Top 3 bidder in 70% of cases** |
| Head-to-head on 20 tenders the company actually bid on | **Better rank than the company in 90% of cases** |

The model also beat the classic statistical bidding models (Gates–Friedman), a baseline that averages past margins, and a regression model. These are **retrospective** results. On highly competitive tenders, the model sometimes recommends margins below the profitability threshold, so an expert still reviews each recommendation.


