# UrbanCart Growth & Analytics Case Study: Data & AI Engineer

Take-home case study submission for the **Data & AI Engineer (Growth & Analytics)** role at UrbanCart. 

This project analyzes marketing channel performance across Jan–Jun 2026, provides strategic reallocation recommendations to cut paid spend by 20%, and designs an automated daily cloud pipeline with a self-serve conversational agent for executive decision-making.

---

## 📂 Repository Structure

```text
├── README.md                      # Project documentation and reproduction guide
├── Case_Study_Brief.pdf           # Original assignment brief
├── data/                          # Source datasets
│   ├── users.csv                  # User signups and channel attribution
│   ├── orders.csv                 # Order values, timestamps, and delivery statuses
│   └── marketing_spend.csv        # Monthly spend by paid channel
└── submission/                    # Required submission deliverables
    ├── Part1_Analysis.ipynb       # Python notebook: Cleaning, CAC, LTV, trends & sanity checks
    ├── Part2_Memo.md              # Executive memo to the CMO (≤250 words) with charts
    ├── Part3_Design.md            # Production architecture and Text-to-SQL tool design (≤400 words)
    ├── AI_Use_Note.md             # Disclosure of AI tools used, rationale, and corrections
    ├── Reflection.md              # Role reflection aligned with the job description (200–250 words)
    └── charts/                    # Exported high-resolution visualization artifacts
        ├── chart1_cac_trend.png   # Monthly CAC inflation (Jan–Jun 2026)
        └── chart2_ltv_cac.png     # Unit economics return on spend (LTV:CAC)
